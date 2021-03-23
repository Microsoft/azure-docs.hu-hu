---
title: Gyors útmutató – Azure Key Vault Python ügyféloldali kódtár – tanúsítványok kezelése
description: Ismerje meg, hogyan hozhat létre, kérhet le és törölhet tanúsítványokat egy Azure Key vaultból a Python ügyféloldali kódtár használatával
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: 2026b1721dbe05af1ecc52324d1dd0ebf2ec3d08
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802658"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-python"></a>Gyors útmutató: Azure Key Vault Certificate Library for Python

Ismerkedés az Azure Key Vault Certificate Pythonhoz készült ügyféloldali kódtáraval. Az alábbi lépéseket követve telepítse a csomagot, és próbálja ki az alapszintű feladatokhoz tartozó kódot. Ha Key Vault használatával tárolja a tanúsítványokat, a kódban nem tárolja a tanúsítványokat, ami növeli az alkalmazás biztonságát.

[API-referenciák dokumentációja](/python/api/overview/azure/keyvault-certificates-readme)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)  |  [Csomag (Python-csomag indexe)](https://pypi.org/project/azure-keyvault-certificates)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python 2.7 + vagy 3.6 +](/azure/developer/python/configure-local-development-environment)
- [Azure CLI](/cli/azure/install-azure-cli)

Ez a rövid útmutató azt feltételezi, hogy az [Azure CLI](/cli/azure/install-azure-cli) -t egy Linux-terminál ablakban futtatja.

## <a name="set-up-your-local-environment"></a>A helyi környezet beállítása

Ez a rövid útmutató az Azure Identity Library és az Azure CLI használatával hitelesíti a felhasználókat az Azure-szolgáltatásokban. A fejlesztők a Visual studiót vagy a Visual Studio Code-ot is használhatják a hívások hitelesítéséhez, további információért lásd: [az ügyfél hitelesítése az Azure Identity Client Library](/python/api/overview/azure/identity-readme) segítségével

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

1. Futtassa a következő parancsot: `login`.

    ```azurecli-interactive
    az login
    ```

    Ha a parancssori felület megnyithatja az alapértelmezett böngészőt, akkor az egy Azure-beli bejelentkezési oldal betöltésével végezhető el.

    Ellenkező esetben nyisson meg egy böngészőt, [https://aka.ms/devicelogin](https://aka.ms/devicelogin) és adja meg a terminálon megjelenő engedélyezési kódot.

2. A böngészőben jelentkezzen be fiókja hitelesítő adataival.

### <a name="install-the-packages"></a>A csomagok telepítése

1. Egy terminálon vagy parancssorban hozzon létre egy megfelelő Project-mappát, majd hozzon létre és aktiváljan egy Python virtuális környezetet a [Python virtuális környezetek használata](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments) című cikkben leírtak szerint.

1. Az Azure Active Directory Identity Library telepítése:

    ```terminal
    pip install azure.identity
    ```


1. Telepítse a Key Vault Certificate Client Library könyvtárat:

    ```terminal
    pip install azure-keyvault-certificates
    ```

### <a name="create-a-resource-group-and-key-vault"></a>Erőforráscsoport és kulcstartó létrehozása

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### <a name="grant-access-to-your-key-vault"></a>Hozzáférés biztosítása a kulcstartóhoz

Hozzon létre egy hozzáférési szabályzatot a kulcstartó számára, amely engedélyt ad a felhasználói fiókjának

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --certificate-permissions delete get list create
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

A Azure Key Vault-tanúsítvány Pythonhoz készült ügyféloldali kódtára lehetővé teszi a tanúsítványok kezelését. Az alábbi mintakód bemutatja, hogyan lehet ügyfelet létrehozni, tanúsítványt beállítani, tanúsítványt lekérni és tanúsítványt törölni.

Hozzon létre egy *kv_certificates.* -es nevű fájlt, amely tartalmazza ezt a kódot.

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

Győződjön meg arról, hogy az előző szakaszban található kód egy *kv_certificates.* a. file nevű fájlban van. Ezután futtassa a kódot a következő paranccsal:

```terminal
python kv_certificates.py
```

- Ha az engedélyek hibába ütközik, győződjön meg arról, hogy a [ `az keyvault set-policy` parancsot](#grant-access-to-your-key-vault)futtatta.
- A kód újbóli futtatása ugyanazzal a kulcsnévvel lehet, hogy a (z) "(ütközés)" tanúsítvány <name> jelenleg törölve van, de helyreállítható állapotban van. " Használjon másik kulcsnévt.

## <a name="code-details"></a>Kód részletei

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása

Ebben a rövid útmutatóban a bejelentkezett felhasználó a Key Vault hitelesítésére szolgál, amely a helyi fejlesztés előnyben részesített módszere. Az Azure-ban üzembe helyezett alkalmazások esetében a felügyelt identitást App Service vagy virtuális géphez kell rendelni, további információért lásd: a [felügyelt identitás áttekintése](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Az alábbi példában a kulcstartó neve a Key Vault URI-ra van kibontva, a "https:// \<your-key-vault-name\> . Vault.Azure.net" formátumban. Ez a példa a  ["DefaultAzureCredential ()"](/python/api/azure-identity/azure.identity.defaultazurecredential) osztályt használja, amely lehetővé teszi, hogy ugyanazt a kódot használja különböző környezetekben különböző beállításokkal az identitás biztosításához. További információ: az Azure-beli [hitelesítő adatok alapértelmezett hitelesítése](https://docs.microsoft.com/python/api/overview/azure/identity-readme). 

```python
credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>Tanúsítvány mentése

Miután megszerezte a Key Vault ügyfél-objektumát, létrehozhat egy tanúsítványt a [begin_create_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#begin-create-certificate-certificate-name--policy----kwargs-) metódus használatával: 

```python
policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()
```

Itt a tanúsítványhoz a [CertificatePolicy.get_default](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificatepolicy?#get-default--) metódussal kapott szabályzat szükséges.

A metódus meghívásával `begin_create_certificate` aszinkron hívást generál a kulcstartó Azure-REST API. Az aszinkron hívás egy Poller objektumot ad vissza. A művelet eredményének megvárnia a Poller metódusának meghívásához `result` .

A kérelem kezelésekor az Azure hitelesíti a hívó identitását (az egyszerű szolgáltatásnév) az ügyfél számára megadott hitelesítőadat-objektum használatával.


### <a name="retrieve-a-certificate"></a>Tanúsítvány lekérése

Key Vault tanúsítványának beolvasásához használja a [get_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#get-certificate-certificate-name----kwargs-) metódust:

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

Azt is ellenőrizheti, hogy a tanúsítvány be van-e állítva az Azure CLI-paranccsal az Key [Vault Certificate show](/cli/azure/keyvault/certificate?#az-keyvault-certificate-show)lehetőséggel.

### <a name="delete-a-certificate"></a>Tanúsítvány törlése

A tanúsítvány törléséhez használja a [begin_delete_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#begin-delete-certificate-certificate-name----kwargs-) metódust:

```python
poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()
```

A `begin_delete_certificate` metódus aszinkron, és egy Poller objektumot ad vissza. A Poller metódusának meghívása `result` megvárja a befejezését.

Ellenőrizze, hogy a tanúsítvány törölve lett-e az Azure CLI-paranccsal az Key [Vault Certificate show](/cli/azure/keyvault/certificate?#az-keyvault-certificate-show)parancs használatával.

A törlés után a tanúsítvány törölve marad, de egy ideig is helyreállítható állapotban van. Ha újra futtatja a kódot, használjon másik nevet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a [titkokkal](../secrets/quick-create-python.md) és [kulcsokkal](../keys/quick-create-python.md)is kísérletezni szeretne, a cikkben létrehozott Key Vault is felhasználhatja.

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
