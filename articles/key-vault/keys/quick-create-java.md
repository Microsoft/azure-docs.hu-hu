---
title: Gyors útmutató – a Java-Azure Key Vault fő ügyféloldali kódtára
description: Gyors útmutató a Azure Key Vault Keys-hez készült ügyféloldali kódtár Javához.
author: msmbaldwin
ms.custom: devx-track-java, devx-track-azurecli
ms.author: mbaldwin
ms.date: 01/05/2021
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.openlocfilehash: cb5abf59c446ef0835375bac45d1e852144a6f28
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97935274"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-java"></a>Gyors útmutató: Azure Key Vault fő ügyféloldali kódtár a Javához
Ismerkedjen meg a Javához készült Azure Key Vault Key ügyféloldali kódtár szolgáltatással. Az alábbi lépéseket követve telepítse a csomagot, és próbálja ki az alapszintű feladatokhoz tartozó kódot.

További források:

* [Forráskód](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys)
* [API-referenciadokumentáció](https://azure.github.io/azure-sdk-for-java/keyvault.html)
* [Termékdokumentáció](index.yml)
* [Példák](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys/src/samples/java/com/azure/security/keyvault/keys)

## <a name="prerequisites"></a>Előfeltételek
- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- A [Java Development Kit (JDK)](/java/azure/jdk/) 8-as vagy újabb verziója
- [Apache Maven](https://maven.apache.org)
- [Azure CLI](/cli/azure/install-azure-cli)

Ez a rövid útmutató azt feltételezi, hogy az [Azure CLI](/cli/azure/install-azure-cli) -t és az [Apache mavent](https://maven.apache.org) egy Linux-terminál ablakban futtatja.

## <a name="setting-up"></a>Beállítás
Ez a rövid útmutató az Azure Identity Library és az Azure CLI használatával hitelesíti a felhasználót az Azure-szolgáltatásokban. A fejlesztők a Visual studiót vagy a Visual Studio Code-ot is használhatják a hívások hitelesítéséhez. További információ: [az ügyfél hitelesítése az Azure Identity Client Library](/java/api/overview/azure/identity-readme)használatával.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
1. Futtassa a következő parancsot: `login`.

    ```azurecli-interactive
    az login
    ```

   Ha a parancssori felület megnyithatja az alapértelmezett böngészőt, akkor az egy Azure-beli bejelentkezési oldal betöltésével végezhető el.

   Ellenkező esetben nyisson meg egy böngészőt, [https://aka.ms/devicelogin](https://aka.ms/devicelogin) és adja meg a terminálon megjelenő engedélyezési kódot.

2. A böngészőben jelentkezzen be fiókja hitelesítő adataival.

### <a name="create-a-new-java-console-app"></a>Új Java-konzol alkalmazás létrehozása
A konzol ablakban a `mvn` parancs használatával hozzon létre egy új Java-konzol alkalmazást a névvel `akv-keys-java` .

```console
mvn archetype:generate -DgroupId=com.keyvault.keys.quickstart
                       -DartifactId=akv-keys-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

A projekt generálásának kimenete a következőképpen fog kinézni:

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
Nyissa meg a *pom.xml* fájlt a szövegszerkesztőben. Adja hozzá az alábbi függőségi elemeket a függőségek csoportjához.

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

#### <a name="grant-access-to-your-key-vault"></a>Hozzáférés biztosítása a kulcstartóhoz
Hozzon létre egy hozzáférési szabályzatot a kulcstartóhoz, amely a felhasználói fiók számára biztosít kulcsfontosságú engedélyeket.

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --key-permissions delete get list create purge
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

## <a name="object-model"></a>Objektummodell
A Java-hoz készült Azure Key Vault Key ügyféloldali kódtár lehetővé teszi a kulcsok kezelését. A [példák](#code-examples) az ügyfelek létrehozására, a kulcsok létrehozására, a kulcsok lekérésére és a kulcsok törlésére mutatnak.

A teljes konzolos alkalmazás [alább](#sample-code)látható.

## <a name="code-examples"></a>Kódpéldák
### <a name="add-directives"></a>Irányelvek hozzáadása
Adja hozzá a következő irányelveket a kód elejéhez:

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
Ebben a rövid útmutatóban egy bejelentkezett felhasználót használ a Key Vault hitelesítésére, amely a helyi fejlesztés előnyben részesített módszere. Az Azure-ban üzembe helyezett alkalmazások esetében felügyelt identitást kell hozzárendelni egy App Service vagy virtuális géphez. További információ: a [felügyelt identitás áttekintése](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Az alábbi példában a kulcstartó neve a Key Vault URI-ra van kibontva, a "https:// \<your-key-vault-name\> . Vault.Azure.net" formátumban. Ez a példa a ["DefaultAzureCredential ()"](https://docs.microsoft.com/java/api/com.azure.identity.defaultazurecredential) osztályt használja, amely lehetővé teszi, hogy ugyanazt a kódot használja különböző környezetekben különböző beállításokkal az identitás biztosításához. További információ: az Azure-beli [hitelesítő adatok alapértelmezett hitelesítése](https://docs.microsoft.com/java/api/overview/azure/identity-readme).

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

KeyClient keyClient = new KeyClientBuilder()
    .vaultUrl(keyVaultUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="create-a-key"></a>Kulcs létrehozása
Most, hogy az alkalmazás hitelesítése megtörtént, létrehozhat egy kulcsot a Key vaultban a `keyClient.createKey` metódus használatával. Ehhez meg kell adni a kulcs nevét és a kulcs típusát – a "myKey" értéket hozzá kell rendelni a `keyName` változóhoz, és egy RSA-t kell használnia `KeyType` ebben a példában.

```java
keyClient.createKey(keyName, KeyType.RSA);
```

Ellenőrizze, hogy a kulcs be van-e állítva az az [kulcstartó Key show](/cli/azure/keyvault/key?#az-keyvault-key-show) paranccsal:

```azurecli
az keyvault key show --vault-name <your-unique-key-vault-name> --name myKey
```

### <a name="retrieve-a-key"></a>Kulcs lekérése
Most már lekérheti a korábban létrehozott kulcsot a `keyClient.getKey` metódussal.

```java
KeyVaultKey retrievedKey = keyClient.getKey(keyName);
 ```

Most már elérheti a beolvasott kulcs részleteit olyan műveletekkel `retrievedKey.getProperties` , mint a, `retrievedKey.getKeyOperations` stb.

### <a name="delete-a-key"></a>Kulcs törlése
Végül törölje a kulcsot a Key vaultból a `keyClient.beginDeleteKey` metódussal.

A kulcs törlése hosszú ideig futó művelet, amellyel lekérdezheti az előrehaladását, vagy megvárhatja, hogy befejeződjön.

```java
SyncPoller<DeletedKey, Void> deletionPoller = keyClient.beginDeleteKey(keyName);
deletionPoller.waitForCompletion();
```

Ellenőrizze, hogy a kulcsot törölték-e az az [kulcstartó Key show](/cli/azure/keyvault/key?#az-keyvault-key-show) paranccsal:

```azurecli
az keyvault key show --vault-name <your-unique-key-vault-name> --name myKey
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, használhatja az Azure CLI-t vagy Azure PowerShell a kulcstartó és a hozzá tartozó erőforráscsoport eltávolításához.

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
Ebben a rövid útmutatóban létrehozta a Key vaultot, létrehozta a kulcsot, lekérte, majd törölte. Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- [A Azure Key Vault áttekintése](../general/overview.md)
- Olvassa el a [Key Vault biztonsági áttekintést](../general/security-overview.md)
- Tekintse [meg a Azure Key Vault fejlesztői útmutatóját](../general/developers-guide.md)
- [A Key vaulthoz való hozzáférés biztonságossá tétele](../general/secure-your-key-vault.md)