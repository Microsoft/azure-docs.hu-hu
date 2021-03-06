---
title: Rövid útmutató – Azure Key Vault Python ügyféloldali kódtár létrehozása – tanúsítványok kezelése
description: Megtudhatja, hogyan hozhat létre, olvashat be és törölhet tanúsítványokat egy Azure-kulcstartóból a Python ügyféloldali kódtár használatával
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: b980a74ebd4fd072c5e04089a737cb387d968f74
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814423"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-python"></a>Rövid útmutató: Azure Key Vault Pythonhoz készült tanúsítvány ügyféloldali kódtárának létrehozása

Első lépések a Pythonhoz Azure Key Vault tanúsítvány ügyféloldali kódtárával. Kövesse az alábbi lépéseket a csomag telepítéséhez, és próbálja ki az alapszintű feladatokhoz szükséges példakódot. A tanúsítványok Key Vault használatával elkerülheti, hogy tanúsítványokat tároljon a kódban, ami növeli az alkalmazás biztonságát.

[API-referenciadokumentáció](/python/api/overview/azure/keyvault-certificates-readme)  |  [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)  |  [Csomag (Python-csomagindex)](https://pypi.org/project/azure-keyvault-certificates)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyenesen.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Python 2.7+ vagy 3.6+](/azure/developer/python/configure-local-development-environment)
- [Azure CLI](/cli/azure/install-azure-cli)

Ez a rövid útmutató feltételezi, hogy az [Azure CLI-t](/cli/azure/install-azure-cli) egy Linux terminálablakban futtatja.

## <a name="set-up-your-local-environment"></a>A helyi környezet beállítása

Ez a rövid útmutató az Azure Identity Library és az Azure CLI használatával hitelesíti a felhasználókat az Azure-szolgáltatásokban. A fejlesztők a Visual Studio vagy Visual Studio Code használatával is hitelesítik a hívásokat. További információ: Az ügyfél hitelesítése az Azure Identity ügyféloldali [kódtárával](/python/api/overview/azure/identity-readme)

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

1. Futtassa a következő parancsot: `login`.

    ```azurecli-interactive
    az login
    ```

    Ha a CLI meg tudja nyitni az alapértelmezett böngészőt, akkor ezt fogja megtenni, és betölt egy Azure bejelentkezési oldalt.

    Ellenkező esetben nyisson meg egy böngészőoldalt a oldalon, és írja be a terminálban [https://aka.ms/devicelogin](https://aka.ms/devicelogin) megjelenő engedélyezési kódot.

2. A böngészőben jelentkezzen be fiókja hitelesítő adataival.

### <a name="install-the-packages"></a>A csomagok telepítése

1. Egy terminálban vagy parancssorban hozzon létre egy megfelelő projektmappát, majd hozzon létre és aktivál egy Python virtuális környezetet a Python virtuális [környezetek használata](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments)

1. Telepítse a Azure Active Directory identitástárát:

    ```terminal
    pip install azure.identity
    ```


1. Telepítse a Key Vault tanúsítvány ügyféloldali kódtárát:

    ```terminal
    pip install azure-keyvault-certificates
    ```

### <a name="create-a-resource-group-and-key-vault"></a>Erőforráscsoport és kulcstartó létrehozása

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### <a name="grant-access-to-your-key-vault"></a>Hozzáférés megadása a kulcstartóhoz

Hozzáférési szabályzat létrehozása a kulcstartóhoz, amely tanúsítványengedélyt ad a felhasználói fióknak

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --certificate-permissions delete get list create
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

A Azure Key Vault Pythonhoz készült tanúsítvány ügyféloldali kódtára lehetővé teszi a tanúsítványok kezelését. A következő kódminta bemutatja, hogyan lehet ügyfelet létrehozni, tanúsítványt beállítani, tanúsítványt lekérni és tanúsítványt törölni.

Hozzon létre egy *kv_certificates.py nevű fájlt,* amely tartalmazza ezt a kódot.

```python
import os
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)

certificateName = input("Input a name for your certificate > ")

print(f"Creating a certificate in {keyVaultName} called '{certificateName}' ...")

policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()

print(" done.")

print(f"Retrieving your certificate from {keyVaultName}.")

retrieved_certificate = client.get_certificate(certificateName)

print(f"Certificate with name '{retrieved_certificate.name}' was found'.")
print(f"Deleting your certificate from {keyVaultName} ...")

poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>A kód futtatása

Győződjön meg arról, hogy az előző szakaszban található kód egy *kv_certificates.py nevű fájlban található.* Ezután futtassa a kódot a következő paranccsal:

```terminal
python kv_certificates.py
```

- Ha engedélyekkel kapcsolatos hibákba ütközik, ellenőrizze, hogy futtatta-e a [ `az keyvault set-policy` parancsot.](#grant-access-to-your-key-vault)
- A kód azonos kulcsnévvel való újrafuttatása a következő hibaüzenetet okozhatja: "(Ütközés) A tanúsítvány jelenleg <name> törölt, de helyreállítható állapotban van." Használjon másik kulcsnevet.

## <a name="code-details"></a>Kód részletei

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása

Ebben a rövid útmutatóban a bejelentkezett felhasználó hitelesítést használ a Key Vaultban, amely a helyi fejlesztés előnyben részesített módszere. Az Azure-ban üzembe helyezett alkalmazások esetében a felügyelt identitást App Service virtuális géphez kell hozzárendelni. További információ: Felügyelt identitások [áttekintése.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Az alábbi példában a kulcstartó neve ki van bontva a key vault URI-nevére, "https:// \<your-key-vault-name\> .vault.azure.net". Ez a példa a  ["DefaultAzureCredential()"](/python/api/azure-identity/azure.identity.defaultazurecredential) osztályt használja, amely lehetővé teszi, hogy ugyanazt a kódot használja különböző környezetekben különböző identitásbeállításokkal. További információ: Alapértelmezett [Azure-beli hitelesítőadat-hitelesítés.](https://docs.microsoft.com/python/api/overview/azure/identity-readme) 

```python
credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>Tanúsítvány mentése

Miután beszerezta a kulcstartó ügyfélobjektumát, létrehozhat egy tanúsítványt az [begin_create_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#begin-create-certificate-certificate-name--policy----kwargs-) metódussal: 

```python
policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()
```

Itt a tanúsítványhoz szükség van egy házirendre, amelyet a CertificatePolicy.get_default [be.](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificatepolicy?#get-default--)

A `begin_create_certificate` metódusok hívása aszinkron hívást hoz létre az Azure REST API a key vault számára. Az aszinkron hívás egy poller objektumot ad vissza. A művelet eredményének megvárása érdekében hívja meg a poller `result` metódusát.

A kérés kezelésekor az Azure az ügyfélnek megadott hitelesítő objektummal hitelesíti a hívó identitását (a szolgáltatásnévvel).


### <a name="retrieve-a-certificate"></a>Tanúsítvány lekérése

A tanúsítvány beolvassa a Key Vault, használja a [get_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#get-certificate-certificate-name----kwargs-) metódust:

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

Azt is ellenőrizheti, hogy a tanúsítvány be lett-e állítva az [az keyvault certificate show Azure CLI-paranccsal.](/cli/azure/keyvault/certificate?#az_keyvault_certificate_show)

### <a name="delete-a-certificate"></a>Tanúsítvány törlése

Tanúsítvány törléséhez használja [](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#begin-delete-certificate-certificate-name----kwargs-) a begin_delete_certificate metódust:

```python
poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()
```

A `begin_delete_certificate` metódus aszinkron, és egy poller objektumot ad vissza. A poller metódusának `result` hívása megvárja a befejezést.

A tanúsítványt az [az keyvault certificate show](/cli/azure/keyvault/certificate?#az_keyvault_certificate_show)Azure CLI-paranccsal ellenőrizheti.

A törlés után a tanúsítvány egy ideig törölt, de helyreállítható állapotban marad. Ha újra futtatja a kódot, használjon egy másik tanúsítványnevet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a titkos kulcsokkal [](../keys/quick-create-python.md)és kulcsokkal is kísérletezni szeretne, újból felhasználhatja Key Vault cikkben létrehozott titkos kulcsokat. [](../secrets/quick-create-python.md)

Ellenkező esetben, ha végzett a cikkben létrehozott erőforrásokkal, a következő paranccsal törölheti az erőforráscsoportot és annak összes tartalmazott erőforrását:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Következő lépések

- [Az Azure Key Vault áttekintése](../general/overview.md)
- [Biztonságos hozzáférés egy kulcstartóhoz](../general/security-features.md)
- [Azure Key Vault fejlesztői útmutató](../general/developers-guide.md)
- [Key Vault biztonsági áttekintése](../general/security-features.md)
- [Hitelesítés Key Vault](../general/authentication.md)
