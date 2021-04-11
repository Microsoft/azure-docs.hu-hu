---
title: 'Rövid útmutató: az első Java-lekérdezés'
description: Ebben a rövid útmutatóban a következő lépésekkel engedélyezheti a Javához készült Resource Graph Maven-csomagokat, és futtathatja az első lekérdezést.
ms.date: 03/30/2021
ms.topic: quickstart
ms.custom: devx-track-java
ms.openlocfilehash: 97c04cb8b8180034bdc5109446c79deb56e457c9
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223964"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-java"></a>Rövid útmutató: az első Resource Graph-lekérdezés futtatása a Java használatával

Az Azure Resource Graph használatának első lépéseként ellenőriznie kell, hogy telepítve vannak-e a szükséges Maven-csomagok a Javához. Ez a rövid útmutató végigvezeti a Maven-csomagok Java-telepítéshez való hozzáadásának folyamatán.

A folyamat végén hozzá kell adni a Maven-csomagokat a Java-telepítéshez, és futtatnia kell az első Resource Graph-lekérdezést.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

- Győződjön meg arról, hogy a legújabb Azure CLI telepítve van (legalább **2.21.0**). Ha még nincs telepítve, tekintse meg [Az Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört.

  > [!NOTE]
  > Az Azure parancssori felület szükséges ahhoz, hogy a Javához készült Azure SDK használhassa a **CLI-alapú hitelesítést** a következő példákban. További információ az egyéb lehetőségekről: [Az Azure Identity ügyféloldali kódtára a Javához](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity).

- A [Java fejlesztői készlet](/azure/developer/java/fundamentals/java-jdk-long-term-support), verzió
  8.

- [Apache Maven](https://maven.apache.org/), 3,6-es vagy újabb verzió.

## <a name="create-the-resource-graph-project"></a>Az erőforrás-gráf projekt létrehozása

Ha engedélyezni szeretné a Java számára az Azure Resource Graph lekérdezését, hozzon létre és konfiguráljon új alkalmazást a Maven használatával, és telepítse a szükséges Maven-csomagokat.

1. Inicializáljon egy "argQuery" nevű új Java-alkalmazást egy [Maven archetípustal](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html):

   ```cmd
   mvn -B archetype:generate -DarchetypeGroupId="org.apache.maven.archetypes" -DgroupId="com.Fabrikam" -DartifactId="argQuery"
   ```

1. Módosítsa a címtárakat az új projekt mappájába `argQuery` , és nyissa meg a `pom.xml` kedvenc szerkesztőjét. Adja hozzá a következő `<dependency>` csomópontokat a meglévő `<dependencies>` csomóponthoz:

   ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-identity</artifactId>
        <version>1.2.4</version>
    </dependency>
    <dependency>
        <groupId>com.azure.resourcemanager</groupId>
        <artifactId>azure-resourcemanager-resourcegraph</artifactId>
        <version>1.0.0-beta.1</version>
    </dependency>
   ```

1. A `pom.xml` fájlban adja hozzá a következő `<properties>` csomópontot az `<project>` Alapcsomópont alatt a forrás-és a célként megadott verziók frissítéséhez:

   ```xml
   <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. A `pom.xml` fájlban adja hozzá a következő `<build>` csomópontot az `<project>` Alapcsomópont alatt a cél és a Főosztály konfigurálásához a projekt futtatásához.

   ```xml
   <build>
       <plugins>
           <plugin>
               <groupId>org.codehaus.mojo</groupId>
               <artifactId>exec-maven-plugin</artifactId>
               <version>1.2.1</version>
               <executions>
                   <execution>
                       <goals>
                           <goal>java</goal>
                       </goals>
                   </execution>
               </executions>
               <configuration>
                   <mainClass>com.Fabrikam.App</mainClass>
               </configuration>
           </plugin>
       </plugins>
   </build>
   ```

1. Cserélje le az alapértelmezett értéket `App.java` `\argQuery\src\main\java\com\Fabrikam` a következő kódra, és mentse a frissített fájlt:

   ```java
   package com.Fabrikam;
   
   import java.util.Arrays;
   import java.util.List;
   import com.azure.core.management.AzureEnvironment;
   import com.azure.core.management.profile.AzureProfile;
   import com.azure.identity.DefaultAzureCredentialBuilder;
   import com.azure.resourcemanager.resourcegraph.ResourceGraphManager;
   import com.azure.resourcemanager.resourcegraph.models.QueryRequest;
   import com.azure.resourcemanager.resourcegraph.models.QueryRequestOptions;
   import com.azure.resourcemanager.resourcegraph.models.QueryResponse;
   import com.azure.resourcemanager.resourcegraph.models.ResultFormat;
   
   public class App
   {
       public static void main( String[] args )
       {
           List<String> listSubscriptionIds = Arrays.asList(args[0]);
           String strQuery = args[1];
   
           ResourceGraphManager manager = ResourceGraphManager.authenticate(new DefaultAzureCredentialBuilder().build(), new AzureProfile(AzureEnvironment.AZURE));
   
           QueryRequest queryRequest = new QueryRequest()
               .withSubscriptions(listSubscriptionIds)
               .withQuery(strQuery);
           
           QueryResponse response = manager.resourceProviders().resources(queryRequest);
   
           System.out.println("Records: " + response.totalRecords());
           System.out.println("Data:\n" + response.data());
       }
   }
   ```

1. A `argQuery` konzol alkalmazás összeállítása:

   ```bash
   mvn package
   ```

## <a name="run-your-first-resource-graph-query"></a>Az első Resource Graph-lekérdezés futtatása

A Java-konzolon létrehozott alkalmazás ideje kipróbálni egy egyszerű Resource Graph-lekérdezést. A lekérdezés az első öt Azure-erőforrást adja vissza az egyes erőforrások **nevével** és **erőforrás-típusával** .

Minden egyes hívásban olyan `argQuery` változót használunk, amelyet a saját értékeivel kell helyettesíteni:

- `{subscriptionId}` – Cserélje le az előfizetése azonosítójára
- `{query}` – Cserélje le az Azure Resource Graph-lekérdezésre

1. A hitelesítéshez használja az Azure CLI-t `az login` .

1. Módosítsa a könyvtárakat az `argQuery` előző paranccsal létrehozott projekt mappájába `mvn -B archetype:generate` .

1. Futtassa az első Azure Resource Graph-lekérdezést a Maven használatával a konzol alkalmazás fordításához, és adja át az argumentumokat. A `exec.args` tulajdonság szóközök alapján azonosítja az argumentumokat. A lekérdezés egyetlen argumentumként való azonosításához egyetlen idézőjelekkel () kell becsomagolni `'` .

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | limit 5'"
   ```

   > [!NOTE]
   > Mivel ez a lekérdezési példa nem tartalmaz olyan rendezési módosítót, mint a `order by` , a lekérdezés többszöri futtatása valószínűleg egy másik erőforrás-készletet eredményez.

1. Módosítsa az argumentumot, `argQuery.exe` és módosítsa a lekérdezést `order by` a **Name (név** ) tulajdonságra:

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | limit 5 | order by name asc'"
   ```

   > [!NOTE]
   > Csakúgy, mint az első lekérdezésnél, e lekérdezés többszöri futtatása esetén is valószínűleg minden kéréssel eltérő erőforráslistát fog kapni. Fontos a lekérdezési parancsok sorrendje. Ebben a példában az `order by` a `limit` után következik. Ez a parancs először a lekérdezés eredményeit korlátozza, majd megrendeli azokat.

1. Módosítsa a végső paramétert, `argQuery.exe` és módosítsa a lekérdezést a `order by` **Name (név** ) tulajdonságra, majd az első `limit` öt találatra:

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | order by name asc | limit 5'"
   ```

Ha a végső lekérdezés többször is fut, feltételezve, hogy a környezetében semmi sem változik, a visszaadott eredmények konzisztensek és a **Name** tulajdonság szerint vannak rendezve, de továbbra is az első öt találatra korlátozódnak.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné távolítani a Java-konzol alkalmazást és a telepített csomagokat, ezt a Project mappa törlésével teheti meg `argQuery` .

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Java-konzol alkalmazást a szükséges Resource Graph-csomagokkal, és futtatta az első lekérdezést. Ha többet szeretne megtudni az erőforrás-gráf nyelvéről, folytassa a lekérdezés nyelvének részletei lapon.

> [!div class="nextstepaction"]
> [További információ a lekérdezési nyelvről](./concepts/query-language.md)