---
title: Az Apache Maven használata Java HBase-ügyfél létrehozásához az Azure HDInsight
description: Ismerje meg, hogyan hozhat létre egy Java-alapú Apache HBase-alkalmazást az Apache Maven használatával, majd hogyan helyezheti üzembe az Azure HDInsight-HBase.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seodec18, devx-track-java
ms.date: 12/24/2019
ms.openlocfilehash: 13616cf99db39ec4dac1d13e3dcd2cefc5a44614
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942959"
---
# <a name="build-java-applications-for-apache-hbase"></a>Java-alkalmazások készítése Apache HBase

Megtudhatja, hogyan hozhat létre egy [Apache HBase](https://hbase.apache.org/) -alkalmazást Java-ban. Ezután használja az alkalmazást a HBase az Azure HDInsight.

A jelen dokumentumban leírt lépések az [Apache Maven](https://maven.apache.org/) használatával létrehozzák és felépítik a projektet. A Maven egy szoftveres projektmenedzsment és-szövegértési eszköz, amely lehetővé teszi a Java-projektekhez tartozó szoftverek, dokumentációk és jelentések készítését.

## <a name="prerequisites"></a>Előfeltételek

* Egy Apache HBase-fürt a HDInsight-on. Lásd: [az Apache HBase első lépései](./apache-hbase-tutorial-get-started-linux.md).

* A [Java Developer Kit (JDK) 8-as verziója](/azure/developer/java/fundamentals/java-jdk-long-term-support).

* Az [Apache Maven](https://maven.apache.org/download.cgi) megfelelően [van telepítve](https://maven.apache.org/install.html) az Apache-ban.  A Maven egy projekt-összeállítási rendszer Java-projektekhez.

* Egy SSH-ügyfél. További információért lásd: [Csatlakozás a HDInsighthoz (Apache Hadoop) SSH-val](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Ha a PowerShellt használja, szüksége lesz az az [modulra](/powershell/azure/).

* Egy szövegszerkesztő. Ez a cikk a Microsoft Jegyzettömb alkalmazást használja.

## <a name="test-environment"></a>Tesztkörnyezet

A cikkben használt környezet a Windows 10 rendszert futtató számítógép volt.  A parancsok végrehajtása egy parancssorban történt, a különböző fájlok pedig a Jegyzettömb alkalmazásban lettek szerkesztve. Ennek megfelelően módosítsa a környezetét.

A parancssorba írja be az alábbi parancsokat egy működő környezet létrehozásához:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Maven-projekt létrehozása

1. Adja meg a következő parancsot egy **hbaseapp** nevű Maven-projekt létrehozásához:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    cd hbaseapp
    mkdir conf
    ```

    Ez a parancs létrehoz egy nevű könyvtárat `hbaseapp` az aktuális helyen, amely egy alapszintű Maven-projektet tartalmaz. A második parancs módosítja a munkakönyvtárat a következőre: `hbaseapp` . A harmadik parancs létrehoz egy új könyvtárat, `conf` amelyet később fog használni. A `hbaseapp` könyvtár a következő elemeket tartalmazza:

    * `pom.xml`: A Project Object Model ([Pom](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) a projekt felépítéséhez használt információkat és konfigurációs adatokat tartalmazza.
    * `src\main\java\com\microsoft\examples`: Az alkalmazás kódját tartalmazza.
    * `src\test\java\com\microsoft\examples`: Az alkalmazáshoz tartozó teszteket tartalmazza.

2. Távolítsa el a generált példa kódját. Törölje a generált teszt-és alkalmazásfájl-fájlokat `AppTest.java` , és `App.java` írja be az alábbi parancsokat:

    ```cmd
    DEL src\main\java\com\microsoft\examples\App.java
    DEL src\test\java\com\microsoft\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>A projekt-objektum modell frissítése

A pom.xml fájl teljes hivatkozását a következő témakörben tekintheti meg: https://maven.apache.org/pom.html .  A megnyitásához `pom.xml` írja be az alábbi parancsot:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Függőségek hozzáadása

A alkalmazásban `pom.xml` adja hozzá a következő szöveget a `<dependencies>` szakaszhoz:

```xml
<dependency>
    <groupId>org.apache.hbase</groupId>
    <artifactId>hbase-shaded-client</artifactId>
    <version>1.1.2</version>
</dependency>
<dependency>
    <groupId>org.apache.phoenix</groupId>
    <artifactId>phoenix-core</artifactId>
    <version>4.14.1-HBase-1.1</version>
</dependency>
```  

Ez a szakasz azt jelzi, hogy a projektnek szüksége van a **hbase-Client** és a **Phoenix-Core** összetevőkre. A fordítás ideje alatt ezek a függőségek az alapértelmezett Maven-tárházból tölthetők le. A [Maven központi tárházának keresésével](https://search.maven.org/artifact/org.apache.hbase/hbase-client/1.1.2/jar) további információkat tudhat meg a függőségről.

> [!IMPORTANT]  
> A hbase verziószámának meg kell egyeznie a HDInsight-fürthöz megadott Apache HBase verziójával. A következő táblázat segítségével megkeresheti a megfelelő verziószámot.

| HDInsight-fürt verziója | Apache HBase-verzió használata |
| --- | --- |
| 3,6 | 1.1.2 |
| 4,0 | 2.0.0 |

A HDInsight-verziókkal és-összetevőkkel kapcsolatos további információkért tekintse meg [a HDInsight-ben elérhető különböző Apache Hadoop összetevőket](../hdinsight-component-versioning.md).

### <a name="build-configuration"></a>Buildelési konfiguráció

A Maven beépülő moduljai lehetővé teszik a projekt összeállítási szakaszainak testreszabását. Ez a szakasz beépülő modulok, erőforrások és egyéb Build-konfigurációs beállítások hozzáadására szolgál.

Adja hozzá a következő kódot a `pom.xml` fájlhoz, majd mentse és zárjuk be a fájlt. Ennek a szövegnek a címkén belül kell lennie a `<project>...</project>` fájlban, például a és a között `</dependencies>` `</project>` .

```xml
<build>
    <sourceDirectory>src</sourceDirectory>
    <resources>
    <resource>
        <directory>${basedir}/conf</directory>
        <filtering>false</filtering>
        <includes>
        <include>hbase-site.xml</include>
        </includes>
    </resource>
    </resources>
    <plugins>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
        <configuration>
            <source>1.8</source>
            <target>1.8</target>
        </configuration>
        </plugin>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <configuration>
        <transformers>
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
            </transformer>
        </transformers>
        </configuration>
        <executions>
        <execution>
            <phase>package</phase>
            <goals>
            <goal>shade</goal>
            </goals>
        </execution>
        </executions>
    </plugin>
    </plugins>
</build>
```

Ez a szakasz egy olyan erőforrást ( `conf/hbase-site.xml` ) konfigurál, amely a HBase konfigurációs adatait tartalmazza.

> [!NOTE]  
> A konfigurációs értékeket kód használatával is megadhatja. Tekintse meg a példában szereplő megjegyzéseket `CreateTable` .

Ez a szakasz az [Apache Maven Compiler beépülő](https://maven.apache.org/plugins/maven-compiler-plugin/) modult és az [Apache Maven Shade beépülő modulját](https://maven.apache.org/plugins/maven-shade-plugin/)is konfigurálja. A fordító beépülő modul a topológia fordítására szolgál. Az árnyékolt beépülő modullal megakadályozható a licencek duplikálása a Maven által készített JAR-csomagban. Ez a beépülő modul a HDInsight-fürtön futó "duplikált licencek" hibájának megakadályozására szolgál. A Maven-Shade-beépülő modullal a `ApacheLicenseResourceTransformer` megvalósítás megakadályozza a hibát.

A Maven-Shade-beépülő modul egy über jar-t is létrehoz, amely az alkalmazás által igényelt összes függőséget tartalmazza.

### <a name="download-the-hbase-sitexml"></a>A hbase-site.xml letöltése

A következő paranccsal másolja a HBase-konfigurációt a HBase-fürtről a `conf` könyvtárba. Cserélje le a `CLUSTERNAME` értéket a HDInsight-fürt nevére, majd írja be a parancsot:

```cmd
scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
```

## <a name="create-the-application"></a>Az alkalmazás létrehozása

### <a name="implement-a-createtable-class"></a>CreateTable osztály implementálása

Új fájl létrehozásához és megnyitásához írja be az alábbi parancsot `CreateTable.java` . Új fájl létrehozásához válassza az **Igen** lehetőséget.

```cmd
notepad src\main\java\com\microsoft\examples\CreateTable.java
```

Ezután másolja és illessze be az alábbi Java-kódot az új fájlba. Ezután zárjuk be a fájlt.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HBaseAdmin;
import org.apache.hadoop.hbase.HTableDescriptor;
import org.apache.hadoop.hbase.TableName;
import org.apache.hadoop.hbase.HColumnDescriptor;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.util.Bytes;

public class CreateTable {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Example of setting zookeeper values for HDInsight
    // in code instead of an hbase-site.xml file
    //
    // config.set("hbase.zookeeper.quorum",
    //            "zookeepernode0,zookeepernode1,zookeepernode2");
    //config.set("hbase.zookeeper.property.clientPort", "2181");
    //config.set("hbase.cluster.distributed", "true");
    //
    //NOTE: Actual zookeeper host names can be found using Ambari:
    //curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts"

    //Linux-based HDInsight clusters use /hbase-unsecure as the znode parent
    config.set("zookeeper.znode.parent","/hbase-unsecure");

    // create an admin object using the config
    HBaseAdmin admin = new HBaseAdmin(config);

    // create the table...
    HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
    // ... with two column families
    tableDescriptor.addFamily(new HColumnDescriptor("name"));
    tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
    admin.createTable(tableDescriptor);

    // define some people
    String[][] people = {
        { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
        { "2", "Franklin", "Holtz", "franklin@contoso.com" },
        { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
        { "4", "Rae", "Schroeder", "rae@contoso.com" },
        { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
        { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

    HTable table = new HTable(config, "people");

    // Add each person to the table
    //   Use the `name` column family for the name
    //   Use the `contactinfo` column family for the email
    for (int i = 0; i< people.length; i++) {
        Put person = new Put(Bytes.toBytes(people[i][0]));
        person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
        person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
        person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
        table.put(person);
    }
    // flush commits and close the table
    table.flushCommits();
    table.close();
    }
}
```

Ez a kód a `CreateTable` osztály, amely létrehoz egy nevű táblát, `people` és feltölti azt néhány előre definiált felhasználóval.

### <a name="implement-a-searchbyemail-class"></a>SearchByEmail osztály implementálása

Új fájl létrehozásához és megnyitásához írja be az alábbi parancsot `SearchByEmail.java` . Új fájl létrehozásához válassza az **Igen** lehetőséget.

```cmd
notepad src\main\java\com\microsoft\examples\SearchByEmail.java
```

Ezután másolja és illessze be az alábbi Java-kódot az új fájlba. Ezután zárjuk be a fájlt.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.Scan;
import org.apache.hadoop.hbase.client.ResultScanner;
import org.apache.hadoop.hbase.client.Result;
import org.apache.hadoop.hbase.filter.RegexStringComparator;
import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
import org.apache.hadoop.hbase.util.Bytes;
import org.apache.hadoop.util.GenericOptionsParser;

public class SearchByEmail {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Use GenericOptionsParser to get only the parameters to the class
    // and not all the parameters passed (when using WebHCat for example)
    String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
    if (otherArgs.length != 1) {
        System.out.println("usage: [regular expression]");
        System.exit(-1);
    }

    // Open the table
    HTable table = new HTable(config, "people");

    // Define the family and qualifiers to be used
    byte[] contactFamily = Bytes.toBytes("contactinfo");
    byte[] emailQualifier = Bytes.toBytes("email");
    byte[] nameFamily = Bytes.toBytes("name");
    byte[] firstNameQualifier = Bytes.toBytes("first");
    byte[] lastNameQualifier = Bytes.toBytes("last");

    // Create a regex filter
    RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
    // Attach the regex filter to a filter
    //   for the email column
    SingleColumnValueFilter filter = new SingleColumnValueFilter(
        contactFamily,
        emailQualifier,
        CompareOp.EQUAL,
        emailFilter
    );

    // Create a scan and set the filter
    Scan scan = new Scan();
    scan.setFilter(filter);

    // Get the results
    ResultScanner results = table.getScanner(scan);
    // Iterate over results and print  values
    for (Result result : results ) {
        String id = new String(result.getRow());
        byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
        String firstName = new String(firstNameObj);
        byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
        String lastName = new String(lastNameObj);
        System.out.println(firstName + " " + lastName + " - ID: " + id);
        byte[] emailObj = result.getValue(contactFamily, emailQualifier);
        String email = new String(emailObj);
        System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
    }
    results.close();
    table.close();
    }
}
```

Az `SearchByEmail` osztály a sorok e-mail-cím alapján történő lekérdezésére használható. Mivel egy reguláris kifejezést használ, egy karakterláncot vagy egy reguláris kifejezést is megadhat a osztály használatakor.

### <a name="implement-a-deletetable-class"></a>DeleteTable osztály implementálása

Új fájl létrehozásához és megnyitásához írja be az alábbi parancsot `DeleteTable.java` . Új fájl létrehozásához válassza az **Igen** lehetőséget.

```cmd
notepad src\main\java\com\microsoft\examples\DeleteTable.java
```

Ezután másolja és illessze be az alábbi Java-kódot az új fájlba. Ezután zárjuk be a fájlt.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HBaseAdmin;

public class DeleteTable {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Create an admin object using the config
    HBaseAdmin admin = new HBaseAdmin(config);

    // Disable, and then delete the table
    admin.disableTable("people");
    admin.deleteTable("people");
    }
}
```

Az `DeleteTable` osztály törli az ebben a példában létrehozott HBase-táblákat az osztály által létrehozott tábla letiltásával és eldobásával `CreateTable` .

## <a name="build-and-package-the-application"></a>Az alkalmazás létrehozása és becsomagolása

1. A `hbaseapp` címtárból használja az alábbi parancsot egy olyan jar-fájl létrehozásához, amely tartalmazza az alkalmazást:

    ```cmd
    mvn clean package
    ```

    Ez a parancs egy. jar fájlba hozza létre és csomagolja az alkalmazást.

2. A parancs végrehajtása után a `hbaseapp/target` könyvtár tartalmaz egy nevű fájlt `hbaseapp-1.0-SNAPSHOT.jar` .

   > [!NOTE]  
   > A `hbaseapp-1.0-SNAPSHOT.jar` fájl egy über jar. Ez tartalmazza az alkalmazás futtatásához szükséges összes függőséget.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>A JAR és a Run feladatok (SSH) feltöltése

A következő lépésekkel `scp` másolhatja a jar-t az Apache HBase elsődleges fő csomópontjára a HDInsight-fürtön. A `ssh` parancs ezután csatlakozik a fürthöz, és a példát közvetlenül a fő csomóponton futtatja.

1. Töltse fel a jar-t a fürtbe. Cserélje le a `CLUSTERNAME` értéket a HDInsight-fürt nevére, majd írja be a következő parancsot:

    ```cmd
    scp ./target/hbaseapp-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:hbaseapp-1.0-SNAPSHOT.jar
    ```

2. Kapcsolódjon a HBase-fürthöz. Cserélje le a `CLUSTERNAME` értéket a HDInsight-fürt nevére, majd írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. A Java-alkalmazással létrehozott HBase-tábla létrehozásához használja a következő parancsot az Open SSH-kapcsolatban:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable
    ```

    Ez a parancs létrehoz egy **emberek** nevű HBase-táblázatot, és feltölti azokat az adatokkal.

4. A táblázatban tárolt e-mail-címek kereséséhez használja a következő parancsot:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com
    ```

    A következő eredményeket kapja:

    ```console
    Franklin Holtz - ID: 2
    Franklin Holtz - franklin@contoso.com - ID: 2
    Rae Schroeder - ID: 4
    Rae Schroeder - rae@contoso.com - ID: 4
    Gabriela Ingram - ID: 6
    Gabriela Ingram - gabriela@contoso.com - ID: 6
    ```

5. A tábla törléséhez használja a következő parancsot:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable
    ```

## <a name="upload-the-jar-and-run-jobs-powershell"></a>A JAR és a Run feladatok feltöltése (PowerShell)

A következő lépések az Azure PowerShell az [modul](/powershell/azure/new-azureps-module-az) használatával töltik fel a jar-t az Apache HBase-fürt alapértelmezett tárolójába. A HDInsight-parancsmagok ezután a példák távoli futtatására szolgálnak.

1. Az az modul telepítése és konfigurálása után hozzon létre egy nevű fájlt `hbase-runner.psm1` . A fájl tartalma legyen a következő szöveg:

   ```powershell
    <#
    .SYNOPSIS
    Copies a file to the primary storage of an HDInsight cluster.
    .DESCRIPTION
    Copies a file from a local directory to the blob container for
    the HDInsight cluster.
    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.CreateTable"
    -clusterName "MyHDInsightCluster"

    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
    -clusterName "MyHDInsightCluster"
    -emailRegex "contoso.com"

    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
    -clusterName "MyHDInsightCluster"
    -emailRegex "^r" -showErr
    #>

    function Start-HBaseExample {
    [CmdletBinding(SupportsShouldProcess = $true)]
    param(
    #The class to run
    [Parameter(Mandatory = $true)]
    [String]$className,

    #The name of the HDInsight cluster
    [Parameter(Mandatory = $true)]
    [String]$clusterName,

    #Only used when using SearchByEmail
    [Parameter(Mandatory = $false)]
    [String]$emailRegex,

    #Use if you want to see stderr output
    [Parameter(Mandatory = $false)]
    [Switch]$showErr
    )

    Set-StrictMode -Version 3

    # Is the Azure module installed?
    FindAzure

    # Get the login for the HDInsight cluster
    $creds=Get-Credential -Message "Enter the login for the cluster" -UserName "admin"

    # The JAR
    $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"

    # The job definition
    $jobDefinition = New-AzHDInsightMapReduceJobDefinition `
        -JarFile $jarFile `
        -ClassName $className `
        -Arguments $emailRegex

    # Get the job output
    $job = Start-AzHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzHDInsightJob `
        -ClusterName $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds
    if($showErr)
    {
    Write-Host "STDERR"
    Get-AzHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds `
                -DisplayOutputType StandardError
    }
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds
    }

    <#
    .SYNOPSIS
    Copies a file to the primary storage of an HDInsight cluster.
    .DESCRIPTION
    Copies a file from a local directory to the blob container for
    the HDInsight cluster.
    .EXAMPLE
    Add-HDInsightFile -localPath "C:\temp\data.txt"
    -destinationPath "example/data/data.txt"
    -ClusterName "MyHDInsightCluster"
    .EXAMPLE
    Add-HDInsightFile -localPath "C:\temp\data.txt"
    -destinationPath "example/data/data.txt"
    -ClusterName "MyHDInsightCluster"
    -Container "MyContainer"
    #>

    function Add-HDInsightFile {
        [CmdletBinding(SupportsShouldProcess = $true)]
        param(
            #The path to the local file.
            [Parameter(Mandatory = $true)]
            [String]$localPath,

            #The destination path and file name, relative to the root of the container.
            [Parameter(Mandatory = $true)]
            [String]$destinationPath,

            #The name of the HDInsight cluster
            [Parameter(Mandatory = $true)]
            [String]$clusterName,

            #If specified, overwrites existing files without prompting
            [Parameter(Mandatory = $false)]
            [Switch]$force
        )

        Set-StrictMode -Version 3

        # Is the Azure module installed?
        FindAzure

        # Get authentication for the cluster
        $creds=Get-Credential

        # Does the local path exist?
        if (-not (Test-Path $localPath))
        {
            throw "Source path '$localPath' does not exist."
        }

        # Get the primary storage container
        $storage = GetStorage -clusterName $clusterName

        # Upload file to storage, overwriting existing files if -force was used.
        Set-AzStorageBlobContent -File $localPath `
            -Blob $destinationPath `
            -force:$force `
            -Container $storage.container `
            -Context $storage.context
    }

    function FindAzure {
        # Is there an active Azure subscription?
        $sub = Get-AzSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Connect-AzAccount
        }
    }

    function GetStorage {
        param(
            [Parameter(Mandatory = $true)]
            [String]$clusterName
        )
        $hdi = Get-AzHDInsightCluster -ClusterName $clusterName
        # Does the cluster exist?
        if (!$hdi)
        {
            throw "HDInsight cluster '$clusterName' does not exist."
        }
        # Create a return object for context & container
        $return = @{}
        $storageAccounts = @{}

        # Get storage information
        $resourceGroup = $hdi.ResourceGroup
        $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
        $container=$hdi.DefaultStorageContainer
        $storageAccountKey=(Get-AzStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Get the resource group, in case we need that
        $return.resourceGroup = $resourceGroup
        # Get the storage context, as we can't depend
        # on using the default storage context
        $return.context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        # Get the container, so we know where to
        # find/store blobs
        $return.container = $container
        # Return storage accounts to support finding all accounts for
        # a cluster
        $return.storageAccount = $storageAccountName
        $return.storageAccountKey = $storageAccountKey

        return $return
    }
    # Only export the verb-phrase things
    export-modulemember *-*
   ```

    Ez a fájl két modult tartalmaz:

   * **Add-HDInsightFile** – fájlok a fürtbe való feltöltéséhez használható
   * **Start-HBaseExample** – a korábban létrehozott osztályok futtatásához használatos

2. Mentse a `hbase-runner.psm1` fájlt a `hbaseapp` könyvtárba.

3. Regisztrálja a modulokat a Azure PowerShell. Nyisson meg egy új Azure PowerShell ablakot, és szerkessze az alábbi parancsot úgy, hogy lecseréli a `CLUSTERNAME` fürt nevét. Ezután adja meg a következő parancsokat:

    ```powershell
    cd C:\HDI\hbaseapp
    $myCluster = "CLUSTERNAME"
    Import-Module .\hbase-runner.psm1
    ```

4. A következő parancs használatával töltse fel a `hbaseapp-1.0-SNAPSHOT.jar` fürtöt a fürtbe.

    ```powershell
    Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName $myCluster
    ```

    Ha a rendszer kéri, adja meg a fürt bejelentkezési (admin) nevét és jelszavát. A parancs feltölti a `hbaseapp-1.0-SNAPSHOT.jar` -t a `example/jars` fürt elsődleges tárolójában lévő helyre.

5. Ha a használatával szeretne táblázatot létrehozni `hbaseapp` , használja a következő parancsot:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName $myCluster
    ```

    Ha a rendszer kéri, adja meg a fürt bejelentkezési (admin) nevét és jelszavát.

    Ez a parancs létrehoz egy **személyek** nevű táblát a HBase a HDInsight-fürtön. Ez a parancs nem jelenít meg kimenetet a konzol ablakában.

6. A táblázat bejegyzéseinek megkereséséhez használja a következő parancsot:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName $myCluster -emailRegex contoso.com
    ```

    Ha a rendszer kéri, adja meg a fürt bejelentkezési (admin) nevét és jelszavát.

    Ez a parancs a `SearchByEmail` osztály használatával keres olyan sorokat, amelyekben az `contactinformation` oszlop családja és az `email` oszlop tartalmazza a karakterláncot `contoso.com` . A következő eredményeknek kell megjelennie:

    ```output
    Franklin Holtz - ID: 2
    Franklin Holtz - franklin@contoso.com - ID: 2
    Rae Schroeder - ID: 4
    Rae Schroeder - rae@contoso.com - ID: 4
    Gabriela Ingram - ID: 6
    Gabriela Ingram - gabriela@contoso.com - ID: 6
    ```

    A **fabrikam.com** használata az `-emailRegex` értékhez azokat a felhasználókat adja vissza, akik az e-mail-mezőben **fabrikam.com** rendelkeznek. Keresési kifejezésként reguláris kifejezéseket is használhat. A (z) **^ r** például az "r" betűvel kezdődő e-mail-címeket adja vissza.

7. A tábla törléséhez használja a következő parancsot:

    ```PowerShell
    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName $myCluster
    ```

### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Nincs eredmény vagy váratlan eredmény a Start-HBaseExample használatakor

A `-showErr` (z) paraméter használatával megtekintheti a feladatok futtatásakor létrehozott szabványos hibát (stderr).

## <a name="next-steps"></a>Következő lépések

[Ismerje meg, hogyan használható az az sqlline használata az Apache HBase](apache-hbase-query-with-phoenix.md)