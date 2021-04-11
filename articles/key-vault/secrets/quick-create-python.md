---
title: Gyors útmutató – Azure Key Vault Python ügyféloldali kódtár – titkok kezelése
description: Megtudhatja, hogyan hozhat létre, kérhet le és törölhet titkos kódokat az Azure Key vaultban a Python ügyféloldali kódtár használatával
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: b82c86983bc07f39c1adb5aa741497d8cc3246e9
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967123"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-python"></a>Gyors útmutató: Azure Key Vault titkos ügyféloldali kódtár a Pythonhoz

Ismerkedjen meg a Azure Key Vault Secret ügyféloldali kódtáraval a Pythonhoz. Az alábbi lépéseket követve telepítse a csomagot, és próbálja ki az alapszintű feladatokhoz tartozó kódot. A titkok tárolása Key Vault használatával elkerülhető, hogy a kódban ne tárolja a titkokat, ami növeli az alkalmazás biztonságát.

[API-referenciák dokumentációja](/python/api/overview/azure/keyvault-secrets-readme)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)  |  [Csomag (Python-csomag indexe)](https://pypi.org/project/azure-keyvault-secrets/)

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
    pip install azure-identity
    ```


1. Telepítse a Key Vault Secrets könyvtárat:

    ```terminal
    pip install azure-keyvault-secrets
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

```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="create-the-sample-code"></a>A mintakód létrehozása

A Pythonhoz készült Azure Key Vault Secret ügyféloldali kódtár lehetővé teszi a titkok kezelését. Az alábbi mintakód bemutatja, hogyan lehet ügyfelet létrehozni, titkos kulcsot beolvasni és titkos kulcsot törölni.

Hozzon létre egy *kv_secrets.* -es nevű fájlt, amely tartalmazza ezt a kódot.

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

Győződjön meg arról, hogy az előző szakaszban található kód egy *kv_secrets.* a. file nevű fájlban van. Ezután futtassa a kódot a következő paranccsal:

```terminal
python kv_secrets.py
```

- Ha az engedélyek hibába ütközik, győződjön meg arról, hogy a [ `az keyvault set-policy` parancsot](#grant-access-to-your-key-vault)futtatta.
- A kód újbóli futtatása ugyanazzal a titkos névvel a következő hibaüzenetet eredményezheti: "(ütközés) a titkos kulcs <name> jelenleg törölve, de helyreállítható állapotban van." Használjon másik titkos nevet.

## <a name="code-details"></a>Kód részletei

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása

Ebben a rövid útmutatóban a bejelentkezett felhasználó a Key Vault hitelesítésére szolgál, amely a helyi fejlesztés előnyben részesített módszere. Az Azure-ban üzembe helyezett alkalmazások esetében a felügyelt identitást App Service vagy virtuális géphez kell rendelni, további információért lásd: a [felügyelt identitás áttekintése](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Az alábbi példában a kulcstartó neve a Key Vault URI-ra van kibontva, a "https:// \<your-key-vault-name\> . Vault.Azure.net" formátumban. Ez a példa a  ["DefaultAzureCredential ()"](https://docs.microsoft.com/python/api/azure-identity/azure.identity.defaultazurecredential) osztályt használja, amely lehetővé teszi, hogy ugyanazt a kódot használja különböző környezetekben különböző beállításokkal az identitás biztosításához. További információ: az Azure-beli [hitelesítő adatok alapértelmezett hitelesítése](https://docs.microsoft.com/python/api/overview/azure/identity-readme). 

```python
credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>Titkos kód mentése

Miután megszerezte a Key vaulthoz tartozó ügyfél-objektumot, a [set_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#set-secret-name--value----kwargs-) metódussal tárolhatja a titkos kulcsot: 

```python
client.set_secret(secretName, secretValue)
```

A hívás a `set_secret` kulcstartóhoz tartozó Azure-REST API hívását hívja elő.

A kérelem kezelésekor az Azure hitelesíti a hívó identitását (az egyszerű szolgáltatásnév) az ügyfél számára megadott hitelesítőadat-objektum használatával.

### <a name="retrieve-a-secret"></a>Titkos kód beolvasása

Key Vault titkos kulcsának beolvasásához használja a [get_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#get-secret-name--version-none----kwargs-) metódust:

```python
retrieved_secret = client.get_secret(secretName)
 ```

A titkos érték a ben található `retrieved_secret.value` .

Az Azure CLI-paranccsal is beolvashat egy titkos kulcsot az kulcstartó [Secret show](/cli/azure/keyvault/secret?#az-keyvault-secret-show)parancs használatával.

### <a name="delete-a-secret"></a>Titkos kulcs törlése

A titkos kód törléséhez használja a [begin_delete_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#begin-delete-secret-name----kwargs-) metódust:

```python
poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()
```

A `begin_delete_secret` metódus aszinkron, és egy Poller objektumot ad vissza. A Poller metódusának meghívása `result` megvárja a befejezését.

Ellenőrizheti, hogy a titkos kulcs el lett-e távolítva az Azure CLI-parancs az kulcstartó [Secret show](/cli/azure/keyvault/secret?#az-keyvault-secret-show)paranccsal.

A törlés után a titkos kód törölve marad, de egy ideig visszanyerhető állapotban van. Ha újra futtatja a kódot, használjon másik titkos nevet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a [tanúsítványokkal](../certificates/quick-create-python.md) és [kulcsokkal](../keys/quick-create-python.md)is kísérletezni szeretne, a cikkben létrehozott Key Vault is felhasználhatja.

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
