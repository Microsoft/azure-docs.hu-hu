---
title: Rövid útmutató Azure Key Vault Certificate Client Library – Java
description: Ismerje meg a Java-hoz készült Azure Key Vault-tanúsítvány ügyféloldali függvénytárát a jelen rövid útmutató lépéseivel.
author: msmbaldwin
ms.custom: devx-track-java, devx-track-azurecli
ms.author: mbaldwin
ms.date: 12/18/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.openlocfilehash: 99b8c63060cebeffea0f3473e03b5f49a415230b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936039"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-java-certificates"></a>Gyors útmutató: Azure Key Vault Certificate Client Library for Java (tanúsítványok)
Ismerkedjen meg a Javához készült Azure Key Vault-tanúsítvány ügyféloldali kódtáraval. Az alábbi lépéseket követve telepítse a csomagot, és próbálja ki az alapszintű feladatokhoz tartozó kódot.

További források:

* [Forráskód](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates)
* [API-referenciadokumentáció](https://azure.github.io/azure-sdk-for-java/keyvault.html)
* [Termékdokumentáció](index.yml)
* [Példák](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates/src/samples/java/com/azure/security/keyvault/certificates)

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
A konzol ablakban a `mvn` parancs használatával hozzon létre egy új Java-konzol alkalmazást a névvel `akv-certificates-java` .

```console
mvn archetype:generate -DgroupId=com.keyvault.certificates.quickstart
                       -DartifactId=akv-certificates-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

A projekt generálásának kimenete a következőképpen fog kinézni:

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
Nyissa meg a *pom.xml* fájlt a szövegszerkesztőben. Adja hozzá az alábbi függőségi elemeket a függőségek csoportjához.

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

#### <a name="grant-access-to-your-key-vault"></a>Hozzáférés biztosítása a kulcstartóhoz
Hozzon létre egy hozzáférési szabályzatot a kulcstartó számára, amely engedélyeket biztosít a felhasználói fióknak.

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --certificate-permissions delete get list create purge
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
A Java-hoz készült Azure Key Vault-tanúsítvány lehetővé teszi a tanúsítványok kezelését. A [példák](#code-examples) az ügyfelek létrehozására, a tanúsítványok létrehozására, a tanúsítványok lekérésére és a tanúsítványok törlésére mutatnak.

A teljes konzolos alkalmazás [alább](#sample-code)látható.

## <a name="code-examples"></a>Kódpéldák
### <a name="add-directives"></a>Irányelvek hozzáadása
Adja hozzá a következő irányelveket a kód elejéhez:

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
Ebben a rövid útmutatóban egy bejelentkezett felhasználót használ a Key Vault hitelesítésére, amely a helyi fejlesztés előnyben részesített módszere. Az Azure-ban üzembe helyezett alkalmazások esetében felügyelt identitást kell hozzárendelni egy App Service vagy virtuális géphez. További információ: a [felügyelt identitás áttekintése](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Az alábbi példában a kulcstartó neve a Key Vault URI-ra van kibontva, a "https:// \<your-key-vault-name\> . Vault.Azure.net" formátumban. Ez a példa a ["DefaultAzureCredential ()"](https://docs.microsoft.com/java/api/com.azure.identity.defaultazurecredential) osztályt használja, amely lehetővé teszi, hogy ugyanazt a kódot használja különböző környezetekben különböző beállításokkal az identitás biztosításához. További információ: az Azure-beli [hitelesítő adatok alapértelmezett hitelesítése](https://docs.microsoft.com/java/api/overview/azure/identity-readme).

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

CertificateClient certificateClient = new CertificateClientBuilder()
    .vaultUrl(keyVaultUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="save-a-secret"></a>Titkos kód mentése
Most, hogy az alkalmazás hitelesítése megtörtént, létrehozhat egy tanúsítványt a kulcstartóban a `certificateClient.beginCreateCertificate` metódus használatával. Ehhez a tanúsítványhoz és a tanúsítvány házirendjéhez nevet kell rendelni – a "myCertificate" értéket a `certificateName` mintában lévő változóhoz rendeltük, és alapértelmezett szabályzatot kell használni.

A tanúsítvány létrehozása hosszú ideig futó művelet, amellyel lekérdezheti az előrehaladását, vagy megvárhatja, hogy befejeződjön.

```java
SyncPoller<CertificateOperation, KeyVaultCertificateWithPolicy> certificatePoller =
    certificateClient.beginCreateCertificate(certificateName, CertificatePolicy.getDefault());
certificatePoller.waitForCompletion();
```

A tanúsítványt a létrehozás után a következő hívással szerezheti be:

```java
KeyVaultCertificate createdCertificate = certificatePoller.getFinalResult();
```

### <a name="retrieve-a-certificate"></a>Tanúsítvány lekérése
Most már lekérheti a korábban létrehozott tanúsítványt a `certificateClient.getCertificate` metódussal.

```java
KeyVaultCertificate retrievedCertificate = certificateClient.getCertificate(certificateName);
 ```

Most már elérheti a beolvasott tanúsítvány részleteit a (z `retrievedCertificate.getName` ), stb. hasonló műveletekkel `retrievedCertificate.getProperties` . Valamint annak tartalma `retrievedCertificate.getCer` .

### <a name="delete-a-certificate"></a>Tanúsítvány törlése
Végül törölje a tanúsítványt a kulcstartóból a `certificateClient.beginDeleteCertificate` metódussal, amely szintén hosszú ideig futó művelet.

```java
SyncPoller<DeletedCertificate, Void> deletionPoller = certificateClient.beginDeleteCertificate(certificateName);
deletionPoller.waitForCompletion();
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
Ebben a rövid útmutatóban létrehozott egy kulcstartót, létrehozott egy tanúsítványt, lekérte, majd törölte. Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- [A Azure Key Vault áttekintése](../general/overview.md)
- Tekintse [meg a Azure Key Vault fejlesztői útmutatóját](../general/developers-guide.md)
- [A Key vaulthoz való hozzáférés biztonságossá tétele](../general/secure-your-key-vault.md)