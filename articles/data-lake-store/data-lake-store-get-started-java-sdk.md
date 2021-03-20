---
title: Java SDK – fájlrendszerbeli műveletek Data Lake Storage Gen1-Azure-ban
description: A Java SDK for Azure Data Lake Storage Gen1 használatával olyan fájlrendszerbeli műveleteket hajthat végre, mint például a mappák létrehozása, valamint az adatfájlok feltöltése és letöltése Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.custom: devx-track-java
ms.author: twooley
ms.openlocfilehash: a2c55a2d3277bbb6c3cf72f5ea703780d2a5e9bd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87318844"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-java-sdk"></a>Filesystem-műveletek Azure Data Lake Storage Gen1 a Java SDK használatával
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Megtudhatja, hogyan végezhet el olyan alapvető műveleteket a Azure Data Lake Storage Gen1 Java SDK használatával, mint például a mappák létrehozása, az adatfájlok feltöltése és letöltése stb. További információ a Data Lake Storage Gen1ről: [Azure Data Lake Storage Gen1](data-lake-store-overview.md).

Az Data Lake Storage Gen1 Java SDK API-docs a [Azure Data Lake Storage Gen1 Java API-docs](https://azure.github.io/azure-data-lake-store-java/javadoc/)webhelyen érhető el.

## <a name="prerequisites"></a>Előfeltételek
* Java-fejlesztőkészlet (JDK 7 vagy újabb, Java 1.7 vagy újabb verzió használatával)
* Data Lake Storage Gen1 fiók. Kövesse a [Azure Data Lake Storage Gen1 használatának első lépései a Azure Portal használatával](data-lake-store-get-started-portal.md)című témakör utasításait.
* [Maven](https://maven.apache.org/install.html). Ez az oktatóanyag a Mavent használja a build- és projektfüggőségek kezeléséhez. Bár lehetséges olyan rendszerek nélkül fejleszteni, mint például a Maven vagy a Gradle, ezekkel a fejlesztőrendszerekkel sokkal egyszerűbb a függőségek kezelése.
* (Nem kötelező) [IntelliJ IDEA](https://www.jetbrains.com/idea/download/), [Eclipse](https://www.eclipse.org/downloads/) vagy hasonló integrált fejlesztőkörnyezet.

## <a name="create-a-java-application"></a>Java-alkalmazás létrehozása
A [GitHubon](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) elérhető kódminta végigvezeti a fájlok tárolóban való létrehozásának, a fájlok összetűzésének, a fájlok letöltésének és az egyes fájlok tárolóból való törlésének folyamatán. A cikk ezen szakasza a kód fő részeit mutatja be.

1. Hozzon létre egy Maven-projektet az [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) használatával parancssorból vagy egy IDE használatával. A Java-projektek IntelliJ használatával való létrehozási útmutatójáért [kattintson ide](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). A projektek Eclipse használatával való létrehozási útmutatójáért [kattintson ide](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm). 

2. Illessze be a következő függőségeket a Maven **pom.xml** nevű fájljába. Adja hozzá a következő kódrészletet a **\</project>** címke elé:
   
    ```xml
    <dependencies>
        <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-data-lake-store-sdk</artifactId>
        <version>2.1.5</version>
        </dependency>
        <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-nop</artifactId>
        <version>1.7.21</version>
        </dependency>
    </dependencies>
    ```
   
    Az első függőség a Data Lake Storage Gen1 SDK () használata a `azure-data-lake-store-sdk` Maven adattárból. A második függőség az alkalmazással használandó naplózási keretrendszer (`slf4j-nop`) meghatározása. A Data Lake Storage Gen1 SDK a [SLF4J](https://www.slf4j.org/) -naplózási homlokzatot használja, amely számos népszerű naplózási keretrendszer közül választhat, például Log4j, Java-naplózás, Logback stb., vagy nincs naplózás. Ebben a példában kikapcsoljuk a naplózást, mivel az **slf4j-nop** kötést eszközt használjuk. Az alkalmazásban való egyéb naplózási lehetőségek használatáról [itt talál információt](https://www.slf4j.org/manual.html#projectDep).

3. Adja hozzá az alábbi importálási utasításokat az alkalmazáshoz.

    ```java
    import com.microsoft.azure.datalake.store.ADLException;
    import com.microsoft.azure.datalake.store.ADLStoreClient;
    import com.microsoft.azure.datalake.store.DirectoryEntry;
    import com.microsoft.azure.datalake.store.IfExists;
    import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
    import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;

    import java.io.*;
    import java.util.Arrays;
    import java.util.List;
    ```

## <a name="authentication"></a>Hitelesítés

* Az alkalmazás végfelhasználói hitelesítéséhez lásd: [végfelhasználói hitelesítés a Data Lake Storage Gen1 Java használatával](data-lake-store-end-user-authenticate-java-sdk.md).
* Az alkalmazás szolgáltatások közötti hitelesítéséhez lásd: [szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 Java használatával](data-lake-store-service-to-service-authenticate-java.md).

## <a name="create-a-data-lake-storage-gen1-client"></a>Data Lake Storage Gen1-ügyfél létrehozása
A [ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) objektum létrehozásához meg kell adnia a Data Lake Storage Gen1 fiók nevét és a jogkivonat-szolgáltatót, amelyet a Data Lake Storage Gen1 hitelesítése során generált (lásd: [hitelesítés](#authentication) szakasz). A Data Lake Storage Gen1 fiók nevének teljesen minősített tartománynévnek kell lennie. Például cserélje le a **Fill-in-here** parancsot a **mydatalakestoragegen1.azuredatalakestore.net** hasonló értékre.

```java
private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, provider);
```

Az alábbi szakaszokban szereplő kódrészletek néhány gyakori fájlrendszerműveletre mutatnak példát. A **ADLStoreClient** objektum teljes [Data Lake Storage GEN1 Java SDK API-docs](https://azure.github.io/azure-data-lake-store-java/javadoc/) segítségével további műveleteket tekinthet meg.

## <a name="create-a-directory"></a>Könyvtár létrehozása

A következő kódrészlet létrehoz egy címtár-struktúrát a megadott Data Lake Storage Gen1 fiók gyökerében.

```java
// create directory
client.createDirectory("/a/b/w");
System.out.println("Directory created.");
```

## <a name="create-a-file"></a>Fájl létrehozása

Az alábbi kódrészlet egy fájlt (c.txt) hoz létre a könyvtárstruktúrában, és adatokat ír ebbe a fájlba.

```java
// create file and write some content
String filename = "/a/b/c.txt";
OutputStream stream = client.createFile(filename, IfExists.OVERWRITE  );
PrintStream out = new PrintStream(stream);
for (int i = 1; i <= 10; i++) {
    out.println("This is line #" + i);
    out.format("This is the same line (%d), but using formatted output. %n", i);
}
out.close();
System.out.println("File created.");
```

Fájl (d.txt) létrehozásához bájttömbök is használhatók.

```java
// create file using byte arrays
stream = client.createFile("/a/b/d.txt", IfExists.OVERWRITE);
byte[] buf = getSampleContent();
stream.write(buf);
stream.close();
System.out.println("File created using byte array.");
```

Az előző kódrészletben használt `getSampleContent` függvény meghatározása a [GitHubon](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) található minta részeként érhető el. 

## <a name="append-to-a-file"></a>Hozzáfűzés fájlhoz

Az alábbi kódrészlet egy meglévő fájlhoz fűzi hozzá a tartalmakat.

```java
// append to file
stream = client.getAppendStream(filename);
stream.write(getSampleContent());
stream.close();
System.out.println("File appended.");
```

Az előző kódrészletben használt `getSampleContent` függvény meghatározása a [GitHubon](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) található minta részeként érhető el.

## <a name="read-a-file"></a>Fájl beolvasása

Az alábbi kódrészlet egy Data Lake Storage Gen1-fiókból származó fájlból olvassa be a tartalmat.

```java
// Read File
InputStream in = client.getReadStream(filename);
BufferedReader reader = new BufferedReader(new InputStreamReader(in));
String line;
while ( (line = reader.readLine()) != null) {
    System.out.println(line);
}
reader.close();
System.out.println();
System.out.println("File contents read.");
```

## <a name="concatenate-files"></a>Fájlok összefűzése

Az alábbi kódrészlet két fájlt fűz össze egy Data Lake Storage Gen1 fiókban. Ha a művelet sikeres, a rendszer az összefűzött fájllal cseréli le a két meglévő fájlt.

```java
// concatenate the two files into one
List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
client.concatenateFiles("/a/b/f.txt", fileList);
System.out.println("Two files concatenated into a new file.");
```

## <a name="rename-a-file"></a>Fájl átnevezése

A következő kódrészlet átnevez egy Data Lake Storage Gen1-fiókban található fájlt.

```java
//rename the file
client.rename("/a/b/f.txt", "/a/b/g.txt");
System.out.println("New file renamed.");
```

## <a name="get-metadata-for-a-file"></a>Fájl metaadatainak lekérése

Az alábbi kódrészlet egy Data Lake Storage Gen1-fiókban lévő fájl metaadatait kérdezi le.

```java
// get file metadata
DirectoryEntry ent = client.getDirectoryEntry(filename);
printDirectoryInfo(ent);
System.out.println("File metadata retrieved.");
```

## <a name="set-permissions-on-a-file"></a>Fájlengedélyek beállítása

Az alábbi kódrészlet az előző szakaszban létrehozott fájlon állít be engedélyeket.

```java
// set file permission
client.setPermission(filename, "744");
System.out.println("File permission set.");
```

## <a name="list-directory-contents"></a>Könyvtár tartalmának listázása

Az alábbi kódrészlet egy könyvtár tartalmát listázza ki rekurzív módon.

```java
// list directory contents
List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
System.out.println("Directory listing for directory /a/b:");
for (DirectoryEntry entry : list) {
    printDirectoryInfo(entry);
}
System.out.println("Directory contents listed.");
```

Az előző kódrészletben használt `printDirectoryInfo` függvény meghatározása a [GitHubon](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) található minta részeként érhető el.

## <a name="delete-files-and-folders"></a>Fájlok és mappák törlése

A következő kódrészlet rekurzív módon törli a megadott fájlokat és mappákat egy Data Lake Storage Gen1 fiókban.

```java
// delete directory along with all the subdirectories and files in it
client.deleteRecursive("/a");
System.out.println("All files and folders deleted recursively");
promptEnterKey();
```

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása
1. Az integrált fejlesztőkörnyezetben történő futtatáshoz keresse meg a **Futtatás** gombot, és kattintson rá. A Mavenben történő futtatáshoz használja az [exec:exec](https://www.mojohaus.org/exec-maven-plugin/exec-mojo.html) beépülő modult.
2. Parancssorból futtatható, különálló jar-fájlt az összes függőség és a [Maven Assembly Plugin](https://maven.apache.org/plugins/maven-assembly-plugin/usage.html) használatával hozhat létre. A [példában a githubon a példa forráskódjának](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml) pom.xml egy példája van.

## <a name="next-steps"></a>Következő lépések
* [A Java SDK JavaDoc-dokumentációjának áttekintése](https://azure.github.io/azure-data-lake-store-java/javadoc/)
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)


