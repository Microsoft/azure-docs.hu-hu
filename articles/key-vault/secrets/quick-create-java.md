---
title: Rövid útmutató – Azure Key Vault Titkos kódtár használata Javához
description: Rövid útmutatót nyújt a Azure Key Vault Java titkos ügyféloldali kódtárához.
author: msmbaldwin
ms.custom: devx-track-java
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: c52ac859f72c440e2cebd59555606c7b3986a314
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814873"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-java"></a>Rövid útmutató: Azure Key Vault Java titkos ügyféloldali kódtárának létrehozása
A Java-hoz Azure Key Vault Secret ügyféloldali kódtár első lépések. A csomag telepítéséhez kövesse az alábbi lépéseket, és próbálja ki az alapszintű feladatokhoz szükséges példakódot.

További források:

* [Forráskód](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)
* [API-referenciadokumentáció](https://azure.github.io/azure-sdk-for-java/keyvault.html)
* [Termékdokumentáció](index.yml)
* [Példák](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets/src/samples/java/com/azure/security/keyvault/secrets)

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
Egy konzolablakban az paranccsal hozzon létre egy új `mvn` Java-konzolalkalmazást `akv-secrets-java` néven.

```console
mvn archetype:generate -DgroupId=com.keyvault.secrets.quickstart
                       -DartifactId=akv-secrets-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

A projekt generálásának kimenete a következő lesz:

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.secrets.quickstart
[INFO] Parameter: artifactId, Value: akv-secrets-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.secrets.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.secrets.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.secrets.quickstart
[INFO] Parameter: artifactId, Value: akv-secrets-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-secrets-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Módosítsa a könyvtárat az újonnan létrehozott `akv-secrets-java/` mappára.

```console
cd akv-secrets-java
```

### <a name="install-the-package"></a>A csomag telepítése
Nyissa meg *pom.xml* fájlt a szövegszerkesztőben. Adja hozzá a következő függőségi elemeket a függőségek csoportjához.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-secrets</artifactId>
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
Hozzon létre egy hozzáférési szabályzatot a kulcstartóhoz, amely titkos engedélyeket ad a felhasználói fióknak.

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --secret-permissions delete get list set purge
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
A Azure Key Vault Secret java ügyféloldali kódtára lehetővé teszi a titkos kódok kezelését. A [Példakódok](#code-examples) szakasz bemutatja, hogyan lehet ügyfelet létrehozni, titkos kódot beállítani, titkos kódot lekérni és titkos kódot törölni.

A teljes konzolalkalmazás a alatt [található.](#sample-code)

## <a name="code-examples"></a>Kódpéldák
### <a name="add-directives"></a>Direktíva hozzáadása
Adja hozzá a következő irányelveket a kód tetejéhez:

```java
import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.DeletedSecret;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret;
```

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása
Ebben a rövid útmutatóban egy bejelentkezett felhasználóval történik a hitelesítés a Key Vault, amely a helyi fejlesztés előnyben részesített módszere. Az Azure-ban üzembe helyezett alkalmazások esetében felügyelt identitást kell hozzárendelni egy App Service virtuális géphez. További információ: [A felügyelt identitás áttekintése.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Az alábbi példában a kulcstartó neve ki van bontva a kulcstartó URI-jába, "https:// \<your-key-vault-name\> .vault.azure.net" formátumban. Ez a példa a ["DefaultAzureCredential()"](https://docs.microsoft.com/java/api/com.azure.identity.defaultazurecredential) osztályt használja, amely lehetővé teszi, hogy ugyanazt a kódot különböző környezetekben használja, különböző identitásbeállításokkal. További információ: Alapértelmezett [Azure-beli hitelesítőadat-hitelesítés.](https://docs.microsoft.com/java/api/overview/azure/identity-readme)

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

SecretClient secretClient = new SecretClientBuilder()
    .vaultUrl(keyVaultUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="save-a-secret"></a>Titkos fájl mentése
Most, hogy az alkalmazás hitelesítve van, a metódussal titkos kulcsot is betehet a `secretClient.setSecret` kulcstartóba. Ehhez szükség van egy névre a titkos nak – ebben a mintában a "mySecret" értéket `secretName` rendeltük hozzá a változóhoz.

```java
secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));
```

A titkos kulcs az [az keyvault secret show](/cli/azure/keyvault/secret?#az_keyvault_secret_show) paranccsal ellenőrizhető:

```azurecli
az keyvault secret show --vault-name <your-unique-key-vault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Titkos adat lekérése
Most már lekérheti a korábban beállított titkos adatokat a `secretClient.getSecret` metódussal.

```java
KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);
 ```

Most már elérheti a lekért titkos adatokat a `retrievedSecret.getValue()` következővel: .

### <a name="delete-a-secret"></a>Titkos kulcs törlése
Végül töröljük a titkos adatokat a kulcstartóból a `secretClient.beginDeleteSecret` metódussal.

A titkos adat törlése egy hosszú ideig futó művelet, amellyel lekérdezheti az előrehaladását, vagy megvárhatja, amíg befejeződik.

```java
SyncPoller<DeletedSecret, Void> deletionPoller = secretClient.beginDeleteSecret(secretName);
deletionPoller.waitForCompletion();
```

A titkos kulcs az [az keyvault secret show](/cli/azure/keyvault/secret?#az_keyvault_secret_show) paranccsal ellenőrizhető:

```azurecli
az keyvault secret show --vault-name <your-unique-key-vault-name> --name mySecret
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
package com.keyvault.secrets.quickstart;

import java.io.Console;

import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.DeletedSecret;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret

public class App {
    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {
        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and key vault URI = %s \n", keyVaultName, keyVaultUri);

        SecretClient secretClient = new SecretClientBuilder()
            .vaultUrl(keyVaultUri)
            .credential(new DefaultAzureCredentialBuilder().build())
            .buildClient();

        Console con = System.console();  

        String secretName = "mySecret";

        System.out.println("Please provide the value of your secret > ");
        
        String secretValue = con.readLine();

        System.out.print("Creating a secret in " + keyVaultName + " called '" + secretName + "' with value '" + secretValue + "` ... ");

        secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));

        System.out.println("done.");
        System.out.println("Forgetting your secret.");
        
        secretValue = "";
        System.out.println("Your secret's value is '" + secretValue + "'.");

        System.out.println("Retrieving your secret from " + keyVaultName + ".");

        KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);

        System.out.println("Your secret's value is '" + retrievedSecret.getValue() + "'.");
        System.out.print("Deleting your secret from " + keyVaultName + " ... ");

        SyncPoller<DeletedSecret, Void> deletionPoller = secretClient.beginDeleteSecret(secretName);
        deletionPoller.waitForCompletion();

        System.out.println("done.");
    }
}
```

## <a name="next-steps"></a>Következő lépések
Ebben a rövid útmutatóban létrehozott egy kulcstartót, tárolt egy titkos kulcsot, lekérte, majd törölte. Ha többet szeretne megtudni a Key Vault és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- Áttekintés a [Azure Key Vault](../general/overview.md)
- Lásd Azure Key Vault [fejlesztői útmutatót](../general/developers-guide.md)
- [Kulcstartóhoz való hozzáférés biztonságossá tere](../general/security-features.md)
