---
title: 'Oktatóanyag: Felügyelt identitás a Key Vault'
description: Felügyelt identitás beállítása egy Key Vault alkalmazáshoz való Azure Spring Cloud való csatlakozáshoz
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/08/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 870a04af244d18826e1041316895f746e27870eb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786584"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-spring-cloud-app"></a>Oktatóanyag: Felügyelt identitás használata Key Vault egy Azure Spring Cloud alkalmazáshoz

**Ez a cikk a következőkre vonatkozik:** ✔️ Java

Ez a cikk bemutatja, hogyan hozhat létre felügyelt identitást egy Azure Spring Cloud alkalmazáshoz, és hogyan használhatja azt a Azure Key Vault.

Azure Key Vault segítségével biztonságosan tárolhatja és szorosan vezérelheti az alkalmazás jogkivonatait, jelszavait, tanúsítványait, API-kulcsait és egyéb titkos kulcsait. Létrehozhat egy felügyelt identitást a Azure Active Directory (AAD) szolgáltatásban, és a hitelesítést bármely olyan szolgáltatásban, amely támogatja az AAD-hitelesítést, beleértve az Key Vault-t is, anélkül, hogy hitelesítő adatokat jeleníten meg a kódban.

## <a name="prerequisites"></a>Előfeltételek

* [Regisztráció Azure-előfizetésre](https://azure.microsoft.com/free/)
* [Az Azure CLI 2.0.67-es vagy újabb verziójának telepítése](/cli/azure/install-azure-cli)
* [A Maven 3.0-s vagy azt feletti telepítése](https://maven.apache.org/download.cgi)

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy erőforráscsoportot, amely tartalmazza a Key Vault és Spring Cloud [az az group create paranccsal:](/cli/azure/group#az_group_create)

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="set-up-your-key-vault"></a>A Key Vault
Hozzon létre egy Key Vault az [az keyvault create paranccsal:](/cli/azure/keyvault#az_keyvault_create)

> [!Important]
> Minden Key Vault egyedi névvel kell rendelkezik. Cserélje <your-keyvault-name> a saját Key Vault a következő példákban.

```azurecli-interactive
az keyvault create --name "<your-keyvault-name>" -g "myResourceGroup"
```

Jegyezze fel a visszaadott adatokat, amelynek formátuma `vaultUri` "https://<your-keyvault-name>.vault.azure.net". Ezt a következő lépésben fogjuk használni.

Most már az [az keyvault secret](/cli/azure/keyvault/secret#az_keyvault_secret_set)set paranccsal Key Vault titkos helyére a titkos kulcsot:

```azurecli-interactive
az keyvault secret set --vault-name "<your-keyvault-name>" \
    --name "connectionString" \
    --value "jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;"
```

## <a name="create-azure-spring-cloud-service-and-app"></a>Új Azure Spring Cloud és alkalmazás létrehozása
A megfelelő bővítmény telepítése után hozzon létre egy Azure Spring Cloud-példányt az Azure CLI-paranccsal. `az spring-cloud create` 

```azurecli-interactive
az extension add --name spring-cloud
az spring-cloud create -n "myspringcloud" -g "myResourceGroup"
```
Az alábbi példa egy nevű alkalmazást hoz létre egy rendszer által hozzárendelt felügyelt `springapp` identitással, a paraméter által `--assign-identity` kértek szerint.

```azurecli
az spring-cloud app create -n "springapp" -s "myspringcloud" -g "myResourceGroup" --assign-endpoint true --assign-identity
export SERVICE_IDENTITY=$(az spring-cloud app show --name "springapp" -s "myspringcloud" -g "myResourceGroup" | jq -r '.identity.principalId')
```

Jegyezze fel a visszaadott `url` `https://<your-app-name>.azuremicroservices.io` formátumot. Ezt a következő lépésben fogjuk használni.


## <a name="grant-your-app-access-to-key-vault"></a>Hozzáférés megadása az alkalmazásnak a Key Vault
A `az keyvault set-policy` használatával biztosítsa a megfelelő Key Vault az alkalmazás számára.
```azurecli
az keyvault set-policy --name "<your-keyvault-name>" --object-id ${SERVICE_IDENTITY} --secret-permissions set get list
```
> [!NOTE]
> A használatával eltávolíthatja az alkalmazáshoz való hozzáférést, miután a `az keyvault delete-policy --name "<your-keyvault-name>" --object-id ${SERVICE_IDENTITY}` rendszer által hozzárendelt felügyelt identitás le van tiltva.

## <a name="build-a-sample-spring-boot-app-with-spring-boot-starter"></a>Mintaalkalmazás Spring Boot a Spring Boot Starter használatával
Ez az alkalmazás hozzáférhet a titkos kulcsok lekért Azure Key Vault. Használja a kezdőalkalmazást: [Azure Key Vault Secrets Spring Boot Starter használatával.](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets)  Azure Key Vault a Spring PropertySource egy **példányaként lesz hozzáadva.**  A tárolóban Azure Key Vault titkos kulcsok kényelmesen elérhetők és használhatók, mint bármely külső konfigurációs tulajdonság, például a fájlok tulajdonságai. 

1. Hozzon létre egy mintaprojektet a start.spring.io a Spring Starter Azure Key Vault. 
    ```azurecli
    curl https://start.spring.io/starter.tgz -d dependencies=web,azure-keyvault-secrets -d baseDir=springapp -d bootVersion=2.3.1.RELEASE -d javaVersion=1.8 | tar -xzvf -
    ```

2. Adja meg a Key Vault az alkalmazásban. 

    ```azurecli
    cd springapp
    vim src/main/resources/application.properties
    ```

    Ha felügyelt identitást szeretne használni Azure Spring Cloud alkalmazásokhoz, adja hozzá az alábbi tartalommal a következő tulajdonságokat az src/main/resources/application.properties fájlhoz.

    ```
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```
    > [!Note] 
    > Hozzá kell adni a key vault URL-címét a `application.properties` fájlban a fentiek szerint. Ellenkező esetben előfordulhat, hogy a rendszer nem rögzíti a Key Vault URL-címét a futtatás során.

3. Adja hozzá a példakódot az src/main/java/com/example/demo/DemoApplication.java webhelyhez. Lekéri a kapcsolati sztringet a Key Vault. 

    ```Java
    package com.example.demo;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.boot.CommandLineRunner;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;

    @SpringBootApplication
    @RestController
    public class DemoApplication implements CommandLineRunner {

        @Value("${connectionString}")
        private String connectionString;

        public static void main(String[] args) {
          SpringApplication.run(DemoApplication.class, args);
        }

        @GetMapping("get")
        public String get() {
          return connectionString;
        }

        public void run(String... varl) throws Exception {
          System.out.println(String.format("\nConnection String stored in Azure Key Vault:\n%s\n",connectionString));
        }
      }
    ```

    Ha megnyitja a pom.xml, látni fogja a `azure-keyvault-secrets-spring-boot-starter` függőségét. Adja hozzá ezt a függőséget a projekthez a pom.xml. 

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-keyvault-secrets-spring-boot-starter</artifactId>
    </dependency>
    ```

4. Csomagolja be a mintaalkalmazást. 

    ```azurecli
    mvn clean package
    ```

5. Most már üzembe helyezheti az alkalmazást az Azure-ban az Azure CLI-paranccsal. `az spring-cloud app deploy` 

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/demo-0.0.1-SNAPSHOT.jar
    ```

6.  Az alkalmazás teszteléséhez használja a nyilvános végpontot vagy a tesztvégpontot.

    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/get
    ```

    A következő üzenet jelenik meg: "Successfully got the value of secret connectionString from Key Vault https://<your-keyvault-name>.vault.azure.net/: jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;".

## <a name="build-sample-spring-boot-app-with-java-sdk"></a>Mintaalkalmazás Spring Boot Java SDK-val

Ez a minta beállít és le tud szerezni titkos Azure Key Vault. A [Azure Key Vault Secret Java-ügyféloldali kódtár](/java/api/overview/azure/security-keyvault-secrets-readme) Azure Active Directory jogkivonat-hitelesítés támogatását az Azure SDK-ban. **TokenCredential** implementációkat biztosít, amelyek segítségével Azure SDK-ügyfeleket lehet összeépíteni az AAD-jogkivonatok hitelesítésének támogatásához.

A Azure Key Vault Titkos kódtár lehetővé teszi a jogkivonatok, jelszavak, API-kulcsok és egyéb titkos kulcsok biztonságos tárolására és szabályozására. A kódtár a titkos kulcsok és verziók létrehozására, lekérésére, frissítésére, törlésére, végleges törlésére, biztonsági mentésére, visszaállítására és listára való műveleteit kínálja.

1. Klónoz egy mintaprojektet. 

    ```azurecli
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. Adja meg a Key Vault az alkalmazásban. 

    ```azurecli
    cd Azure-Spring-Cloud-Samples/managed-identity-keyvault
    vim src/main/resources/application.properties
    ```

    Ha felügyelt identitást szeretne használni Azure Spring Cloud alkalmazásokhoz, adja hozzá a következő tartalommal a tulajdonságokat az *src/main/resources/application.properties fájlhoz.*

    ```
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```

3. Adja [meg a ManagedIdentityCredentialBuildert,](/java/api/com.azure.identity.managedidentitycredentialbuilder) hogy lekérte a Azure Active Directory és a [SecretClientBuilder](/java/api/com.azure.security.keyvault.secrets.secretclientbuilder) jogkivonatát, és titkos kódokat állítson be Key Vault kódból.

    A klónozott mintaprojekt [MainController.java-beli](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/src/main/java/com/microsoft/azure/MainController.java#L28) példáját.

    A és `azure-identity` `azure-security-keyvault-secrets` a is függőségként szerepel a pom.xml. A példa le [pom.xml](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/pom.xml#L21) a klónozott mintaprojektből. 

4. Csomagolja be a mintaalkalmazást. 

    ```azurecli
    mvn clean package
    ```

5. Most telepítse az alkalmazást az Azure-ban az Azure CLI-paranccsal. `az spring-cloud app deploy` 

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/asc-managed-identity-keyvault-sample-0.1.0.jar
    ```

6. Az alkalmazás teszteléséhez használja a nyilvános végpontot vagy a tesztvégpontot. 

    Először szerezze be a titkos secret értékét, amit a Azure Key Vault. 
    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/connectionString
    ```

    A következő üzenet jelenik meg: "Successfully got the value of secret connectionString from Key Vault https://<your-keyvault-name>.vault.azure.net/: jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;".

    Most hozzon létre egy titkos adatokat, majd olvassa be a Java SDK-val. 
    ```azurecli
    curl -X PUT https://myspringcloud-springapp.azuremicroservices.io/secrets/test?value=success

    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/test
    ```

    A következő üzenet jelenik meg: "Successfully got the value of secret test from Key Vault https://<your-keyvault-name>.vault.azure.net: success". 

## <a name="next-steps"></a>Következő lépések

* [Storage-blob elérése felügyelt identitással a Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [Rendszer által hozzárendelt felügyelt identitás engedélyezése egy Azure Spring Cloud számára](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [További információ az Azure-erőforrások felügyelt identitási szolgáltatásról](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Hitelesítés Azure Spring Cloud Key Vault GitHub Actions](./spring-cloud-github-actions-key-vault.md)
