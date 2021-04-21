---
title: Rövid útmutató – Azure Key Vault Kulcs ügyféloldali kódtár használata Javához
description: Gyorsútmutatót biztosít a Azure Key Vault Keys Java-hoz való ügyféloldali kódtárához.
author: msmbaldwin
ms.custom: devx-track-java
ms.author: mbaldwin
ms.date: 01/05/2021
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.openlocfilehash: 75cb7b6c9225e8579561f980df10da8994257133
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777184"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-java"></a>Rövid útmutató: Azure Key Vault Kulcs ügyféloldali kódtár létrehozása Javához
A Java Azure Key Vault ügyféloldali kódtárának első lépések. A csomag telepítéséhez kövesse az alábbi lépéseket, és próbálja ki az alapszintű feladatokhoz szükséges példakódot.

További források:

* [Forráskód](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys)
* [API-referenciadokumentáció](https://azure.github.io/azure-sdk-for-java/keyvault.html)
* [Termékdokumentáció](index.yml)
* [Példák](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys/src/samples/java/com/azure/security/keyvault/keys)

## <a name="prerequisites"></a>Előfeltételek
- Azure-előfizetés – [hozzon létre egyet ingyenesen.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Java fejlesztői készlet (JDK)](/java/azure/jdk/) 8-as vagy újabb verziója
- [Apache Maven](https://maven.apache.org)
- [Azure CLI](/cli/azure/install-azure-cli)

Ez a rövid útmutató azt feltételezi, hogy az [Azure CLI-t](/cli/azure/install-azure-cli) és az [Apache Mavent](https://maven.apache.org) egy Linux terminálablakban futtatja.

## <a name="setting-up"></a>Beállítása
Ez a rövid útmutató az Azure Identity Library és az Azure CLI használatával hitelesíti a felhasználókat az Azure-szolgáltatásokban. A fejlesztők a Visual Studio vagy Visual Studio Code használatával is hitelesítik a hívásokat. További információ: Az ügyfél hitelesítése az Azure Identity ügyféloldali [kódtárával.](/java/api/overview/azure/identity-readme)

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
1. Futtassa a következő parancsot: `login`.

    ```azurecli-interactive
    az login
    ```

   Ha a CLI meg tudja nyitni az alapértelmezett böngészőt, akkor ezt fogja megtenni, és betölt egy Azure bejelentkezési oldalt.

   Ellenkező esetben nyisson meg egy böngészőoldalt a oldalon, és adja meg a terminálon [https://aka.ms/devicelogin](https://aka.ms/devicelogin) megjelenő engedélyezési kódot.

2. A böngészőben jelentkezzen be fiókja hitelesítő adataival.

### <a name="create-a-new-java-console-app"></a>Új Java-konzolalkalmazás létrehozása
Egy konzolablakban az paranccsal hozzon létre egy új `mvn` Java-konzolalkalmazást `akv-keys-java` néven.

```console
mvn archetype:generate -DgroupId=com.keyvault.keys.quickstart
                       -DartifactId=akv-keys-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

A projekt generálásának kimenete a következő lesz:

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.keys.quickstart
[INFO] Parameter: artifactId, Value: akv-keys-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.keys.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.keys.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.keys.quickstart
[INFO] Parameter: artifactId, Value: akv-keys-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-keys-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Módosítsa a könyvtárat az újonnan létrehozott `akv-keys-java/` mappára.

```console
cd akv-keys-java
```

### <a name="install-the-package"></a>A csomag telepítése
Nyissa meg *pom.xml* fájlt a szövegszerkesztőben. Adja hozzá a következő függőségi elemeket a függőségek csoportjához.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-keys</artifactId>
      <version>4.2.3</version>
    </dependency>

    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-identity</artifactId>
      <version>1.2.0</version>
    </dependency>
```

### <a name="create-a-resource-group-and-key-vault"></a>Erőforráscsoport és kulcstartó létrehozása
[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

#### <a name="grant-access-to-your-key-vault"></a>Hozzáférés megadása a kulcstartóhoz
Hozzon létre egy hozzáférési szabályzatot a kulcstartóhoz, amely kulcsengedélyeket ad a felhasználói fióknak.

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --key-permissions delete get list create purge
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

## <a name="object-model"></a>Objektummodell
A Azure Key Vault Key java ügyféloldali kódtára lehetővé teszi a kulcsok kezelését. A [Példakódok](#code-examples) szakasz bemutatja, hogyan hozhat létre ügyfelet, hozhat létre kulcsot, hogyan lehet lekérni és törölni egy kulcsot.

A teljes konzolalkalmazás a alatt [található.](#sample-code)

## <a name="code-examples"></a>Kódpéldák
### <a name="add-directives"></a>Direktíva hozzáadása
Adja hozzá a következő irányelveket a kód tetejéhez:

```java
import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.keys.KeyClient;
import com.azure.security.keyvault.keys.KeyClientBuilder;
import com.azure.security.keyvault.keys.models.DeletedKey;
import com.azure.security.keyvault.keys.models.KeyType;
import com.azure.security.keyvault.keys.models.KeyVaultKey;
```

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása
Ebben a rövid útmutatóban egy bejelentkezett felhasználóval történik a hitelesítés a Key Vault, amely a helyi fejlesztés előnyben részesített módszere. Az Azure-ban üzembe helyezett alkalmazások esetében felügyelt identitást kell hozzárendelni egy App Service virtuális géphez. További információ: [A felügyelt identitás áttekintése.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Az alábbi példában a kulcstartó neve ki van bontva a kulcstartó URI-jába, "https:// \<your-key-vault-name\> .vault.azure.net" formátumban. Ez a példa a ["DefaultAzureCredential()"](https://docs.microsoft.com/java/api/com.azure.identity.defaultazurecredential) osztályt használja, amely lehetővé teszi, hogy ugyanazt a kódot különböző környezetekben használja, különböző identitásbeállításokkal. További információ: Alapértelmezett [Azure-beli hitelesítőadat-hitelesítés.](https://docs.microsoft.com/java/api/overview/azure/identity-readme)

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

KeyClient keyClient = new KeyClientBuilder()
    .vaultUrl(keyVaultUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="create-a-key"></a>Kulcs létrehozása
Most, hogy az alkalmazás hitelesítve van, létrehozhat egy kulcsot a kulcstartóban a `keyClient.createKey` metódussal. Ehhez szükség van a kulcs nevére és egy kulcstípusra – ehhez a "myKey" értéket rendeltük a változóhoz, és `keyName` rsa-t használtunk ebben a `KeyType` mintában.

```java
keyClient.createKey(keyName, KeyType.RSA);
```

A kulcs az [az keyvault key show](/cli/azure/keyvault/key?#az_keyvault_key_show) paranccsal ellenőrizhető:

```azurecli
az keyvault key show --vault-name <your-unique-key-vault-name> --name myKey
```

### <a name="retrieve-a-key"></a>Kulcs lekérése
Most már lekérheti a korábban létrehozott kulcsot a `keyClient.getKey` metódussal.

```java
KeyVaultKey retrievedKey = keyClient.getKey(keyName);
 ```

Most már hozzáférhet a lekért kulcs részleteihez olyan műveletekkel, mint a `retrievedKey.getProperties` , `retrievedKey.getKeyOperations` stb.

### <a name="delete-a-key"></a>Kulcs törlése
Végül töröljük a kulcsot a kulcstartóból a `keyClient.beginDeleteKey` metódussal.

A kulcstörlés egy hosszú ideig futó művelet, amellyel lekérdezheti a folyamat előrehaladását, vagy megvárhatja, amíg befejeződik.

```java
SyncPoller<DeletedKey, Void> deletionPoller = keyClient.beginDeleteKey(keyName);
deletionPoller.waitForCompletion();
```

Az [az keyvault key show](/cli/azure/keyvault/key?#az_keyvault_key_show) paranccsal ellenőrizheti, hogy a kulcs törölve lett-e:

```azurecli
az keyvault key show --vault-name <your-unique-key-vault-name> --name myKey
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, az Azure CLI vagy a Azure PowerShell eltávolíthatja a kulcstartót és a megfelelő erőforráscsoportot.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Mintakód
```java
package com.keyvault.keys.quickstart;

import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.keys.KeyClient;
import com.azure.security.keyvault.keys.KeyClientBuilder;
import com.azure.security.keyvault.keys.models.DeletedKey;
import com.azure.security.keyvault.keys.models.KeyType;
import com.azure.security.keyvault.keys.models.KeyVaultKey;

public class App {
    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {
        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and key vault URI = %s \n", keyVaultName, keyVaultUri);

        KeyClient keyClient = new KeyClientBuilder()
                .vaultUrl(keyVaultUri)
                .credential(new DefaultAzureCredentialBuilder().build())
                .buildClient();

        String keyName = "myKey";

        System.out.print("Creating a key in " + keyVaultName + " called '" + keyName + " ... ");

        keyClient.createKey(keyName, KeyType.RSA);

        System.out.print("done.");
        System.out.println("Retrieving key from " + keyVaultName + ".");

        KeyVaultKey retrievedKey = keyClient.getKey(keyName);

        System.out.println("Your key's ID is '" + retrievedKey.getId() + "'.");
        System.out.println("Deleting your key from " + keyVaultName + " ... ");

        SyncPoller<DeletedKey, Void> deletionPoller = keyClient.beginDeleteKey(keyName);
        deletionPoller.waitForCompletion();

        System.out.print("done.");
    }
}
```

## <a name="next-steps"></a>Következő lépések
Ebben a rövid útmutatóban létrehozott egy kulcstartót, létrehozott egy kulcsot, lekérte, majd törölte. Ha többet szeretne megtudni a Key Vault és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- Áttekintés a [Azure Key Vault](../general/overview.md)
- Olvassa el [a Key Vault áttekintését](../general/security-overview.md)
- Lásd Azure Key Vault [fejlesztői útmutatót](../general/developers-guide.md)
- [Kulcstartóhoz való hozzáférés biztonságossá tere](../general/security-overview.md)