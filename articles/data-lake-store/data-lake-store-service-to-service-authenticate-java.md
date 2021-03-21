---
title: Szolgáltatások közötti hitelesítés – Data Lake Storage Gen1 – Java SDK
description: Ismerje meg, hogyan valósítható meg a szolgáltatások közötti hitelesítés a Azure Data Lake Storage Gen1 használatával a Azure Active Directory Javával
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.custom: devx-track-java
ms.author: twooley
ms.openlocfilehash: 0e320557a7372af6a41038d9b3196db23d2496c3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000385"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Szolgáltatások közötti hitelesítés a Azure Data Lake Storage Gen1 Java használatával

> [!div class="op_single_selector"]
> * [A Java használata](data-lake-store-service-to-service-authenticate-java.md)
> * [A .NET SDK használata](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [A Python használata](data-lake-store-service-to-service-authenticate-python.md)
> * [A REST API használata](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>  

Ebből a cikkből megtudhatja, hogyan használhatja a Java SDK-t a szolgáltatások közötti hitelesítéshez Azure Data Lake Storage Gen1 használatával. A Java SDK használatával történő Data Lake Storage Gen1 végfelhasználói hitelesítés nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

* **Egy Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Hozzon létre egy Azure Active Directory "web" alkalmazást**. A Data Lake Storage Gen1 Azure Active Directory használatával kell végrehajtania a [szolgáltatások közötti hitelesítéshez](data-lake-store-service-to-service-authenticate-using-active-directory.md)szükséges lépéseket.

* [Maven](https://maven.apache.org/install.html). Ez az oktatóanyag a Mavent használja a build- és projektfüggőségek kezeléséhez. Habár lehetséges a kiépítés anélkül, hogy olyan Build rendszert kellene használni, mint a Maven vagy a Gradle, ezek a rendszerek sokkal egyszerűbbé teszik a függőségek kezelését.

* Választható IDE, például [INTELLIJ Idea](https://www.jetbrains.com/idea/download/) vagy [Eclipse](https://www.eclipse.org/downloads/) vagy hasonló.

## <a name="service-to-service-authentication"></a>Szolgáltatások közötti hitelesítés

1. Hozzon létre egy Maven-projektet az [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) használatával parancssorból vagy egy IDE használatával. A Java-projektek IntelliJ használatával való létrehozási útmutatójáért [kattintson ide](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). A projektek Eclipse használatával való létrehozási útmutatójáért [kattintson ide](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm).

2. Illessze be a következő függőségeket a Maven **pom.xml** nevű fájljába. Adja hozzá a következő kódrészletet a **\</project>** címke elé:

    ```xml
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-data-lake-store-sdk</artifactId>
        <version>2.2.3</version>
      </dependency>
      <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-nop</artifactId>
        <version>1.7.21</version>
      </dependency>
    </dependencies>
    ```

    Az első függőség a Data Lake Storage Gen1 SDK () használata a `azure-data-lake-store-sdk` Maven adattárból. A második függőség az alkalmazással használandó naplózási keretrendszer (`slf4j-nop`) meghatározása. A Data Lake Storage Gen1 SDK a [slf4j](https://www.slf4j.org/) -naplózási homlokzatot használja, amely számos népszerű naplózási keretrendszer közül választhat, például Log4j, Java-naplózás, logback stb., vagy nincs naplózás. Ebben a példában kikapcsoljuk a naplózást, mivel az **slf4j-nop** kötést eszközt használjuk. Az alkalmazásban való egyéb naplózási lehetőségek használatáról [itt talál információt](https://www.slf4j.org/manual.html#projectDep).

3. Adja hozzá az alábbi importálási utasításokat az alkalmazáshoz.

    ```java
    import com.microsoft.azure.datalake.store.ADLException;
    import com.microsoft.azure.datalake.store.ADLStoreClient;
    import com.microsoft.azure.datalake.store.DirectoryEntry;
    import com.microsoft.azure.datalake.store.IfExists;
    import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
    import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;
    ```

4. A Java-alkalmazásban az alábbi kódrészlettel szerezheti be a korábban létrehozott Active Directory webalkalmazás jogkivonatát `AccessTokenProvider` (a következő példában a-t használ `ClientCredsTokenProvider` ). A jogkivonat-szolgáltató a memóriában gyorsítótárazza a jogkivonat beszerzéséhez használt hitelesítő adatokat, és automatikusan megújítja azt, ha közeleg a lejárati ideje. Létrehozhat saját alosztályokat is, `AccessTokenProvider` így a tokeneket az ügyfél kódja alapján szerezheti be. Egyelőre csak használja az SDK-ban megadott ilyet.

    Cserélje ki a **FILL-IN-HERE** értéket az Azure Active Directory-webalkalmazáshoz tartozó tényleges értékkel.

    ```java
    private static String clientId = "FILL-IN-HERE";
    private static String authTokenEndpoint = "FILL-IN-HERE";
    private static String clientKey = "FILL-IN-HERE";

    AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);   
    ```

A Data Lake Storage Gen1 SDK kényelmes módszereket biztosít a Data Lake Storage Gen1-fiókkal való kommunikációhoz szükséges biztonsági jogkivonatok kezeléséhez. Azonban az SDK nem írja elő, hogy kizárólag ezek a módszerek használhatók. A jogkivonat beszerzésére más eszközöket is használhat, például az [Active Directory SDK-t](https://github.com/AzureAD/azure-activedirectory-library-for-java) , vagy saját egyéni kódot.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan használható a végfelhasználói hitelesítés a Data Lake Storage Gen1 a Java SDK-val való hitelesítéshez. A következő cikkekből megtudhatja, hogyan használhatja a Java SDK-t a Data Lake Storage Gen1-vel való együttműködésre.

* [Az adatműveletek Data Lake Storage Gen1 a Java SDK használatával](data-lake-store-get-started-java-sdk.md)
