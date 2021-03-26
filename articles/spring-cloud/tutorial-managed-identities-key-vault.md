---
title: 'Oktatóanyag: felügyelt identitás Key Vault kapcsolódáshoz'
description: Felügyelt identitás beállítása Key Vault Azure Spring Cloud-alkalmazáshoz való kapcsolódásához
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/08/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: bbfafeaf9ce24911bb4893748d456d22e02fc411
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879175"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-spring-cloud-app"></a>Oktatóanyag: felügyelt identitás használata a Key Vault Azure Spring Cloud-alkalmazáshoz való összekapcsolásához

**Ez a cikk a következőkre vonatkozik:** ✔️ Java

Ebből a cikkből megtudhatja, hogyan hozhat létre felügyelt identitást egy Azure Spring Cloud-alkalmazáshoz, és hogyan használhatja azt Azure Key Vaulthoz való hozzáféréshez.

Azure Key Vault a tokenekhez, jelszavakhoz, tanúsítványokhoz, API-kulcsokhoz és egyéb titkos kódokhoz való hozzáférés biztonságos tárolásához és szigorú szabályozásához használható. Felügyelt identitást Azure Active Directory (HRE) hozhat létre, és bármely olyan szolgáltatáshoz hitelesítheti magát, amely támogatja a HRE hitelesítést, beleértve a Key Vault is, anélkül, hogy meg kellene adni a hitelesítő adatokat a kódban.

## <a name="prerequisites"></a>Előfeltételek

* [Feliratkozás Azure-előfizetésre](https://azure.microsoft.com/free/)
* [Az Azure CLI 2.0.67 vagy újabb verziójának telepítése](/cli/azure/install-azure-cli)
* [A Maven 3,0-es vagy újabb verziójának telepítése](https://maven.apache.org/download.cgi)

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy erőforráscsoportot, amely a Key Vault és a Spring Cloud karaktert is tartalmazza az az [Group Create](/cli/azure/group#az-group-create): paranccsal.

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="set-up-your-key-vault"></a>A Key Vault beállítása
Key Vault létrehozásához használja a parancsot az a Key [Vault Create](/cli/azure/keyvault#az-keyvault-create):

> [!Important]
> Minden Key Vault egyedi névvel kell rendelkeznie. A következő példákban cserélje le <a-kulcstartó-Name> a Key Vault nevére.

```azurecli-interactive
az keyvault create --name "<your-keyvault-name>" -g "myResourceGroup"
```

Jegyezze fel a visszaadott értéket `vaultUri` , amely a "https://<Your-kulcstartó-name>. Vault.Azure.net" formátumban jelenik meg. Ezt a következő lépésben fogjuk használni.

Most már elhelyezheti a titkos kulcsot a Key Vault a parancs az kulcstartó [Secret set](/cli/azure/keyvault/secret#az-keyvault-secret-set):

```azurecli-interactive
az keyvault secret set --vault-name "<your-keyvault-name>" \
    --name "connectionString" \
    --value "jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;"
```

## <a name="create-azure-spring-cloud-service-and-app"></a>Azure Spring Cloud-szolgáltatás és-alkalmazás létrehozása
A megfelelő bővítmény telepítését követően hozzon létre egy Azure Spring Cloud-példányt az Azure CLI-paranccsal `az spring-cloud create` . 

```azurecli-interactive
az extension add --name spring-cloud
az spring-cloud create -n "myspringcloud" -g "myResourceGroup"
```
Az alábbi példa egy nevű alkalmazást hoz létre `springapp` egy rendszerhez rendelt felügyelt identitással, a paraméter által kért módon `--assign-identity` .

```azurecli
az spring-cloud app create -n "springapp" -s "myspringcloud" -g "myResourceGroup" --assign-endpoint true --assign-identity
export SERVICE_IDENTITY=$(az spring-cloud app show --name "springapp" -s "myspringcloud" -g "myResourceGroup" | jq -r '.identity.principalId')
```

Jegyezze fel a visszaadott értéket `url` , amelynek formátuma a következő lesz: `https://<your-app-name>.azuremicroservices.io` . Ezt a következő lépésben fogjuk használni.


## <a name="grant-your-app-access-to-key-vault"></a>Az alkalmazás hozzáférésének biztosítása Key Vault
A használatával `az keyvault set-policy` megfelelő hozzáférést biztosíthat Key Vault az alkalmazáshoz.
```azurecli
az keyvault set-policy --name "<your-keyvault-name>" --object-id ${SERVICE_IDENTITY} --secret-permissions set get list
```
> [!NOTE]
> A használatával `az keyvault delete-policy --name "<your-keyvault-name>" --object-id ${SERVICE_IDENTITY}` távolíthatja el az alkalmazáshoz való hozzáférést, miután a rendszerhez rendelt felügyelt identitás le van tiltva.

## <a name="build-a-sample-spring-boot-app-with-spring-boot-starter"></a>Minta Spring boot-alkalmazás létrehozása Spring boot Starter-vel
Ez az alkalmazás hozzáférhet a Azure Key Vault titkainak beszerzéséhez. Használja az alapszintű alkalmazást: [Azure Key Vault Secrets Spring boot Starter](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets).  Azure Key Vault a Spring **PropertySource**-példányként lesz hozzáadva.  A Azure Key Vaultban tárolt titkos kulcsok kényelmesen elérhetők és felhasználhatók, mint például a fájlok tulajdonságai. 

1. Start.spring.io létrehozása a Azure Key Vault Spring Starter-vel. 
    ```azurecli
    curl https://start.spring.io/starter.tgz -d dependencies=web,azure-keyvault-secrets -d baseDir=springapp -d bootVersion=2.3.1.RELEASE -d javaVersion=1.8 | tar -xzvf -
    ```

2. Adja meg a Key Vault az alkalmazásban. 

    ```azurecli
    cd springapp
    vim src/main/resources/application.properties
    ```

    Az Azure Spring Cloud apps felügyelt identitásának használatához adja hozzá a tulajdonságokat az alábbi tartalommal a src/Main/Resources/Application. properties tulajdonsághoz.

    ```
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```
    > [!Note] 
    > A Key Vault URL-címét a `application.properties` fentiek szerint kell hozzáadnia. Ellenkező esetben előfordulhat, hogy a Key Vault URL-címe nem lesz rögzítve a futtatókörnyezetben.

3. Adja hozzá a következő kódot a src/Main/Java/com/example/demó/DemoApplication. Java-hoz. Lekéri a Key Vault a kapcsolatok sztringjét. 

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

    Ha megnyit pom.xml, akkor a függőségét fogja látni `azure-keyvault-secrets-spring-boot-starter` . Adja hozzá ezt a függőséget a projekthez pom.xml. 

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-keyvault-secrets-spring-boot-starter</artifactId>
    </dependency>
    ```

4. A minta alkalmazás becsomagolása. 

    ```azurecli
    mvn clean package
    ```

5. Most már üzembe helyezheti az alkalmazást az Azure-ban az Azure CLI paranccsal  `az spring-cloud app deploy` . 

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/demo-0.0.1-SNAPSHOT.jar
    ```

6.  Az alkalmazás teszteléséhez nyissa meg a nyilvános végpontot vagy a tesztelési végpontot.

    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/get
    ```

    A következő üzenet jelenik meg: "sikerült a titkos connectionString érték beolvasása Key Vault https://<saját-kulcstartó-neve>. vault.azure.net/: JDBC: SQLServer://SERVER.database.windows.net: 1433; adatbázis = adatbázis;".

## <a name="build-sample-spring-boot-app-with-java-sdk"></a>Minta tavaszi rendszerindítási alkalmazás létrehozása Java SDK-val

Ez a minta beállíthatók és beszerezhetik a titkokat Azure Key Vaultból. A Java-hoz készült [Azure Key Vault titkos ügyféloldali kódtár](/java/api/overview/azure/security-keyvault-secrets-readme) Azure Active Directory jogkivonat-hitelesítési támogatást biztosít az Azure SDK-ban. Olyan **TokenCredential** -implementációkat biztosít, amelyek segítségével az Azure SDK-ügyfelek az HRE-tokenek hitelesítésének támogatásához használhatók.

A Azure Key Vault titkos ügyféloldali kódtár lehetővé teszi a tokenekhez, jelszavakhoz, API-kulcsokhoz és egyéb titkokhoz való hozzáférés biztonságos tárolását és szabályozását. A függvénytár a titkok és a hozzájuk tartozó verziók létrehozására, lekérésére, frissítésére, törlésére, törlésére, biztonsági mentésére, visszatöltésére és listázására szolgáló műveleteket biztosít.

1. Egy minta projekt klónozása. 

    ```azurecli
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. Adja meg a Key Vault az alkalmazásban. 

    ```azurecli
    cd Azure-Spring-Cloud-Samples/managed-identity-keyvault
    vim src/main/resources/application.properties
    ```

    Az Azure Spring Cloud apps felügyelt identitásának használatához adja hozzá a tulajdonságokat a következő tartalommal a *src/Main/Resources/Application. properties* fájlhoz.

    ```
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```

3. Vegyen fel [ManagedIdentityCredentialBuilder](/java/api/com.azure.identity.managedidentitycredentialbuilder) , hogy Azure Active Directory és [SecretClientBuilder](/java/api/com.azure.security.keyvault.secrets.secretclientbuilder) jogkivonatot szerezzen be, vagy beolvassa a titkokat a kódban Key Vault.

    Szerezze be a példát a klónozott minta projekt [MainController. Java](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/src/main/java/com/microsoft/azure/MainController.java#L28) -ból.

    `azure-identity`A és a `azure-security-keyvault-secrets` pom.xml függőségét is tartalmazza. Példa a klónozott minta projekt [pom.xml](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/pom.xml#L21) . 

4. A minta alkalmazás becsomagolása. 

    ```azurecli
    mvn clean package
    ```

5. Most telepítse az alkalmazást az Azure-ba az Azure CLI paranccsal  `az spring-cloud app deploy` . 

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/asc-managed-identity-keyvault-sample-0.1.0.jar
    ```

6. Az alkalmazás teszteléséhez nyissa meg a nyilvános végpontot vagy a tesztelési végpontot. 

    Először szerezze be a Azure Key Vaultban beállított titok értékét. 
    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/connectionString
    ```

    A következő üzenet jelenik meg: "sikerült a titkos connectionString érték beolvasása Key Vault https://<saját-kulcstartó-neve>. vault.azure.net/: JDBC: SQLServer://SERVER.database.windows.net: 1433; adatbázis = adatbázis;".

    Most hozzon létre egy titkos kulcsot, majd kérje le azt a Java SDK használatával. 
    ```azurecli
    curl -X PUT https://myspringcloud-springapp.azuremicroservices.io/secrets/test?value=success

    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/test
    ```

    A következő üzenet jelenik meg: "sikerült a titkos teszt értéke Key Vault https://<a-kulcstartó-neve>. vault.azure.net: sikeres". 

## <a name="next-steps"></a>Következő lépések

* [A Storage-Blobok elérése felügyelt identitással az Azure Spring Cloud-ban](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [A rendszerhez rendelt felügyelt identitás engedélyezése az Azure Spring Cloud Application szolgáltatáshoz](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [További információ az Azure-erőforrások felügyelt identitásáról](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Az Azure Spring Cloud hitelesítése Key Vaultekkel a GitHub-műveletekben](./spring-cloud-github-actions-key-vault.md)
