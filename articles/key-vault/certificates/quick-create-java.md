---
title: Rövid útmutató a Azure Key Vault ügyféloldali kódtárhoz – Java
description: Ebben a rövid útmutatóban Azure Key Vault megismeri a Javához való Azure Key Vault ügyféloldali kódtárát.
author: msmbaldwin
ms.custom: devx-track-java
ms.author: mbaldwin
ms.date: 12/18/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.openlocfilehash: 47b15c857f6f0c8c4d8f377ee4ecdcca74e88e0f
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818572"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-java-certificates"></a>Rövid útmutató: Azure Key Vault Java-tanúsítvány ügyféloldali kódtárának létrehozása (Tanúsítványok)
Első lépések a Azure Key Vault Java-tanúsítvány ügyféloldali kódtárával. Kövesse az alábbi lépéseket a csomag telepítéséhez, és próbálja ki az alapszintű feladatokhoz szükséges példakódot.

További források:

* [Forráskód](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates)
* [API-referenciadokumentáció](https://azure.github.io/azure-sdk-for-java/keyvault.html)
* [Termékdokumentáció](index.yml)
* [Példák](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates/src/samples/java/com/azure/security/keyvault/certificates)

## <a name="prerequisites"></a>Előfeltételek
- Azure-előfizetés – [hozzon létre egyet ingyenesen.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Java fejlesztői készlet (JDK)](/java/azure/jdk/) 8-as vagy újabb verziója
- [Apache Maven](https://maven.apache.org)
- [Azure CLI](/cli/azure/install-azure-cli)

Ez a rövid útmutató feltételezi, hogy az [Azure CLI-t](/cli/azure/install-azure-cli) és az [Apache Mavent](https://maven.apache.org) egy Linux terminálablakban futtatja.

## <a name="setting-up"></a>Beállítása
Ez a rövid útmutató az Azure Identity Library és az Azure CLI használatával hitelesíti a felhasználókat az Azure-szolgáltatásokban. A fejlesztők a Visual Studio vagy Visual Studio Code használatával is hitelesítik a hívásokat. További információ: Az ügyfél hitelesítése az Azure Identity ügyféloldali [kódtárával.](/java/api/overview/azure/identity-readme)

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
1. Futtassa a következő parancsot: `login`.

    ```azurecli-interactive
    az login
    ```

   Ha a CLI meg tudja nyitni az alapértelmezett böngészőt, akkor ezt fogja megtenni, és betölt egy Azure bejelentkezési oldalt.

   Ellenkező esetben nyisson meg egy böngészőoldalt a oldalon, és írja be a terminálban [https://aka.ms/devicelogin](https://aka.ms/devicelogin) megjelenő engedélyezési kódot.

2. A böngészőben jelentkezzen be fiókja hitelesítő adataival.

### <a name="create-a-new-java-console-app"></a>Új Java-konzolalkalmazás létrehozása
Egy konzolablakban az paranccsal hozzon létre egy új `mvn` Java-konzolalkalmazást `akv-certificates-java` néven.

```console
mvn archetype:generate -DgroupId=com.keyvault.certificates.quickstart
                       -DartifactId=akv-certificates-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

A projekt generálásának kimenete a következőre hasonlít:

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.certificates.quickstart
[INFO] Parameter: artifactId, Value: akv-certificates-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.certificates.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.certificates.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.certificates.quickstart
[INFO] Parameter: artifactId, Value: akv-certificates-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-certificates-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Módosítsa a könyvtárat az újonnan létrehozott `akv-certificates-java/` mappára.

```console
cd akv-certificates-java
```

### <a name="install-the-package"></a>A csomag telepítése
Nyissa meg *pom.xml* fájlt a szövegszerkesztőben. Adja hozzá a következő függőségi elemeket a függőségek csoportjához.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-certificates</artifactId>
      <version>4.1.3</version>
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
Hozzon létre egy hozzáférési szabályzatot a kulcstartóhoz, amely tanúsítványengedélyeket ad a felhasználói fióknak.

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --certificate-permissions delete get list create purge
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
A Azure Key Vault Tanúsítvány ügyféloldali kódtára a Javához lehetővé teszi a tanúsítványok kezelését. A [Példakódok](#code-examples) szakasz bemutatja, hogyan lehet ügyfelet létrehozni, tanúsítványt létrehozni, tanúsítványt lekérni és tanúsítványt törölni.

A teljes konzolalkalmazás a alatt [található.](#sample-code)

## <a name="code-examples"></a>Kódpéldák
### <a name="add-directives"></a>Direktíva hozzáadása
Adja hozzá a következő irányelveket a kód tetejéhez:

```java
import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.certificates.CertificateClient;
import com.azure.security.keyvault.certificates.CertificateClientBuilder;
import com.azure.security.keyvault.certificates.models.CertificateOperation;
import com.azure.security.keyvault.certificates.models.CertificatePolicy;
import com.azure.security.keyvault.certificates.models.DeletedCertificate;
import com.azure.security.keyvault.certificates.models.KeyVaultCertificate;
import com.azure.security.keyvault.certificates.models.KeyVaultCertificateWithPolicy;
```

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása
Ebben a rövid útmutatóban egy bejelentkezett felhasználóval történik a hitelesítés a Key Vault, amely a helyi fejlesztés előnyben részesített módszere. Az Azure-ban üzembe helyezett alkalmazások esetében felügyelt identitást kell hozzárendelni egy App Service virtuális géphez. További információ: [A felügyelt identitás áttekintése.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Az alábbi példában a kulcstartó neve ki van bontva a Key Vault URI-nevére, "https:// \<your-key-vault-name\> .vault.azure.net". Ez a példa a ["DefaultAzureCredential()"](https://docs.microsoft.com/java/api/com.azure.identity.defaultazurecredential) osztályt használja, amely lehetővé teszi, hogy ugyanazt a kódot használja különböző környezetekben különböző beállításokkal az identitások megadása érdekében. További információ: Alapértelmezett [Azure-beli hitelesítőadat-hitelesítés.](https://docs.microsoft.com/java/api/overview/azure/identity-readme)

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

CertificateClient certificateClient = new CertificateClientBuilder()
    .vaultUrl(keyVaultUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="save-a-secret"></a>Titkos gombra
Most, hogy az alkalmazás hitelesítve van, létrehozhat egy tanúsítványt a kulcstartóban a `certificateClient.beginCreateCertificate` metódussal. Ehhez szükség van a tanúsítvány nevére és egy tanúsítvány-házirendre – a mintában a "myCertificate" értéket rendeltük hozzá a változóhoz, és egy alapértelmezett házirendet `certificateName` használtunk.

A tanúsítvány létrehozása hosszú ideig futó művelet, amelynek során lekérdezheti az előrehaladását, vagy megvárhatja, amíg befejeződik.

```java
SyncPoller<CertificateOperation, KeyVaultCertificateWithPolicy> certificatePoller =
    certificateClient.beginCreateCertificate(certificateName, CertificatePolicy.getDefault());
certificatePoller.waitForCompletion();
```

Miután a létrehozás befejeződött, a következő hívással szerezheti be a tanúsítványt:

```java
KeyVaultCertificate createdCertificate = certificatePoller.getFinalResult();
```

### <a name="retrieve-a-certificate"></a>Tanúsítvány lekérése
Most már lekérheti a korábban létrehozott tanúsítványt a `certificateClient.getCertificate` metódussal.

```java
KeyVaultCertificate retrievedCertificate = certificateClient.getCertificate(certificateName);
 ```

Most már hozzáférhet a lekért tanúsítvány részleteihez olyan műveletekkel, mint a `retrievedCertificate.getName` , `retrievedCertificate.getProperties` stb. Valamint a `retrievedCertificate.getCer` tartalma.

### <a name="delete-a-certificate"></a>Tanúsítvány törlése
Végül töröljük a tanúsítványt a kulcstartóból a metódussal, amely szintén egy `certificateClient.beginDeleteCertificate` hosszú ideig futó művelet.

```java
SyncPoller<DeletedCertificate, Void> deletionPoller = certificateClient.beginDeleteCertificate(certificateName);
deletionPoller.waitForCompletion();
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
package com.keyvault.certificates.quickstart;

import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.certificates.CertificateClient;
import com.azure.security.keyvault.certificates.CertificateClientBuilder;
import com.azure.security.keyvault.certificates.models.CertificateOperation;
import com.azure.security.keyvault.certificates.models.CertificatePolicy;
import com.azure.security.keyvault.certificates.models.DeletedCertificate;
import com.azure.security.keyvault.certificates.models.KeyVaultCertificate;
import com.azure.security.keyvault.certificates.models.KeyVaultCertificateWithPolicy;

public class App {
    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {
        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and kv uri = %s \n", keyVaultName, keyVaultUri);

        CertificateClient certificateClient = new CertificateClientBuilder()
                .vaultUrl(keyVaultUri)
                .credential(new DefaultAzureCredentialBuilder().build())
                .buildClient();

        String certificateName = "myCertificate";

        System.out.print("Creating a certificate in " + keyVaultName + " called '" + certificateName + " ... ");

        SyncPoller<CertificateOperation, KeyVaultCertificateWithPolicy> certificatePoller =
                certificateClient.beginCreateCertificate(certificateName, CertificatePolicy.getDefault());
        certificatePoller.waitForCompletion();

        System.out.print("done.");
        System.out.println("Retrieving certificate from " + keyVaultName + ".");

        KeyVaultCertificate retrievedCertificate = certificateClient.getCertificate(certificateName);

        System.out.println("Your certificate's ID is '" + retrievedCertificate.getId() + "'.");
        System.out.println("Deleting your certificate from " + keyVaultName + " ... ");

        SyncPoller<DeletedCertificate, Void> deletionPoller = certificateClient.beginDeleteCertificate(certificateName);
        deletionPoller.waitForCompletion();

        System.out.print("done.");
    }
}
```

## <a name="next-steps"></a>Következő lépések
Ebben a rövid útmutatóban létrehozott egy kulcstartót, létrehozott egy tanúsítványt, lekérte, majd törölte. Ha többet szeretne megtudni a Key Vault és az alkalmazásokba való integrálásáról, folytassa az alábbi cikkekkel.

- Olvassa el [a Azure Key Vault](../general/overview.md)
- Lásd [Azure Key Vault fejlesztői útmutatót](../general/developers-guide.md)
- [Kulcstartóhoz való hozzáférés biztonságossá tere](../general/security-features.md)