---
title: Földrajzilag elosztott megoldás implementálása
description: Megtudhatja, hogyan konfigurálhatja az adatbázist a Azure SQL Database és az ügyfélalkalmazás számára a replikált adatbázisba való feladatátvételhez, valamint a feladatátvételi teszthez.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurecli, devx-track-azurepowershell
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 03/12/2019
ms.openlocfilehash: 89d285a56553f5c521d1edbc92786debd4a92e32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101659290"
---
# <a name="tutorial-implement-a-geo-distributed-database-azure-sql-database"></a>Oktatóanyag: földrajzilag elosztott adatbázis implementálása (Azure SQL Database)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Konfiguráljon egy adatbázist a SQL Database és az ügyfélalkalmazás számára a feladatátvételhez egy távoli régióba, és tesztelje a feladatátvételi tervet. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
>
> - [Feladatátvételi csoport](auto-failover-group-overview.md) létrehozása
> - Java-alkalmazás futtatása SQL Database-adatbázis lekérdezéséhez
> - Feladatátvétel tesztelése

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy telepítette a következő elemeket:

- [Azure PowerShell](/powershell/azure/)
- Egyetlen adatbázis a Azure SQL Databaseban. Egy használat létrehozásához
  - [Az Azure Portal](single-database-create-quickstart.md)
  - [Azure CLI](az-cli-script-samples-content-guide.md)
  - [PowerShell](powershell-script-content-guide.md)

  > [!NOTE]
  > Az oktatóanyag a *AdventureWorksLT* -mintaadatbázis használatával működik.

- A Java és a Maven esetében lásd: [alkalmazás létrehozása SQL Server használatával](https://www.microsoft.com/sql-server/developer-get-started/), a **Java** kiemelése és a környezet kiválasztása, majd kövesse a lépéseket.

> [!IMPORTANT]
> Ügyeljen arra, hogy a tűzfalszabályok azon számítógép nyilvános IP-címének használatára legyenek beállítva, amelyen az oktatóanyag lépéseit végrehajtja. Az adatbázis-szintű tűzfalszabályok automatikusan replikálva lesznek a másodlagos kiszolgálóra.
>
> További információért lásd: [adatbázis-szintű tűzfalszabály létrehozása](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) vagy a kiszolgáló szintű tűzfalszabály által használt IP-cím meghatározása a számítógépen: [kiszolgáló szintű tűzfal létrehozása](firewall-create-server-level-portal-quickstart.md).  

## <a name="create-a-failover-group"></a>Feladatátvételi csoport létrehozása

A Azure PowerShell használatával hozzon létre [feladatátvételi csoportokat](auto-failover-group-overview.md) egy meglévő kiszolgáló és egy másik régió egy új kiszolgálója között. Ezután adja hozzá a mintaadatbázis a feladatátvételi csoporthoz.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

Feladatátvételi csoport létrehozásához futtassa a következő parancsfájlt:

```powershell
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>"
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
New-AzSqlServer -ResourceGroupName $resourceGroup -ServerName $drServer `
    -Location $drLocation -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
    -ArgumentList $admin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# create a failover group between the servers
New-AzSqlDatabaseFailoverGroup –ResourceGroupName $resourceGroup -ServerName $server `
    -PartnerServerName $drServer –FailoverGroupName $failoverGroup –FailoverPolicy Automatic -GracePeriodWithDataLossHours 2

# add the database to the failover group
Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $server -DatabaseName $database | `
    Add-AzSqlDatabaseToFailoverGroup -ResourceGroupName $resourceGroup -ServerName $server -FailoverGroupName $failoverGroup
```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!IMPORTANT]
> Az `az login` Azure-ba való bejelentkezéshez futtassa a parancsot.

```azurecli
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>" # must be different then $location
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
az sql server create --admin-password $password --admin-user $admin `
    --name $drServer --resource-group $resourceGroup --location $drLocation

# create a failover group between the servers
az sql failover-group create --name $failoverGroup --partner-server $drServer `
    --resource-group $resourceGroup --server $server --add-db $database `
    --failover-policy Automatic --grace-period 2
```

* * *

A Geo-replikálási beállítások a Azure Portalban is megváltoztathatók, ha kiválasztja az adatbázist, majd a **Beállítások**  >  **geo-replikálás** lehetőséget.

![Geo-replikálási beállítások](./media/geo-distributed-application-configure-tutorial/geo-replication.png)

## <a name="run-the-sample-project"></a>A minta projekt futtatása

1. A-konzolon hozzon létre egy Maven-projektet a következő paranccsal:

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

1. Írja be az **Y** értéket, és nyomja le az **ENTER billentyűt**

1. Módosítsa a címtárakat az új projektre.

   ```bash
   cd SqlDbSample
   ```

1. A kedvenc szerkesztője segítségével nyissa meg a *pom.xml* fájlt a Project mappában.

1. A következő szakasz hozzáadásával adja hozzá a Microsoft JDBC-illesztőprogramot SQL Server-függőséghez `dependency` . A függőséget a nagyobb szakaszba kell beilleszteni `dependencies` .

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

1. Adja meg a Java verzióját a szakasz `properties` utáni szakasz hozzáadásával `dependencies` :

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. A jegyzékfájlok támogatásához adja hozzá a szakaszt a `build` `properties` szakasz után:

   ```xml
   <build>
     <plugins>
       <plugin>
         <groupId>org.apache.maven.plugins</groupId>
         <artifactId>maven-jar-plugin</artifactId>
         <version>3.0.0</version>
         <configuration>
           <archive>
             <manifest>
               <mainClass>com.sqldbsamples.App</mainClass>
             </manifest>
           </archive>
        </configuration>
       </plugin>
     </plugins>
   </build>
   ```

1. Mentse és zárja be a *pom.xml* fájlt.

1. Nyissa meg a.. mappában található *app. Java* fájlt. \SqlDbSample\src\main\java\com\sqldbsamples és cserélje le a tartalmát a következő kódra:

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.Timestamp;
   import java.sql.DriverManager;
   import java.util.Date;
   import java.util.concurrent.TimeUnit;

   public class App {

      private static final String FAILOVER_GROUP_NAME = "<your failover group name>";  // add failover group name
  
      private static final String DB_NAME = "<your database>";  // add database name
      private static final String USER = "<your admin>";  // add database user
      private static final String PASSWORD = "<your password>";  // add database password

      private static final String READ_WRITE_URL = String.format("jdbc:" +
         "sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:" +
         "sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println("");

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " +
                   (insertData((highWaterMark + i)) ? "successful" : "failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " +
                   (selectData((highWaterMark + i)) ? "successful" : "failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name so we can find the product again
      String sql = "INSERT INTO SalesLT.Product " +
         "(Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         pstmt.setInt(2, 200989 + id + 10000);
         pstmt.setString(3, "Blue");
         pstmt.setDouble(4, 75.00);
         pstmt.setDouble(5, 89.99);
         pstmt.setTimestamp(6, new Timestamp(new Date().getTime()));
         return (1 == pstmt.executeUpdate());
      } catch (Exception e) {
         return false;
      }
   }

   private static boolean selectData(int id) {
      // Query the data previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(READ_ONLY_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         try (ResultSet resultSet = pstmt.executeQuery()) {
            return resultSet.next();
         }
      } catch (Exception e) {
         return false;
      }
   }

   private static int getHighWaterMarkId() {
      // Query the high water mark id stored in the table to be able to make unique inserts
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              Statement stmt = connection.createStatement();
              ResultSet resultSet = stmt.executeQuery(sql)) {
         if (resultSet.next()) {
             result =  resultSet.getInt(1);
            }
         } catch (Exception e) {
          e.printStackTrace();
         }
         return result;
      }
   }
   ```

1. Mentse és zárda be az *app. Java* fájlt.

1. Futtassa a következő parancsot a parancssori konzolon:

   ```bash
   mvn package
   ```

1. Indítsa el azt az alkalmazást, amely körülbelül 1 órára fog futni, amíg manuálisan le nem állítja, így a feladatátvételi teszt futtatásához szükséges idő.

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

   ```output
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ...
   ```

## <a name="test-failover"></a>Feladatátvétel tesztelése

Futtassa a következő parancsfájlokat a feladatátvétel szimulálásához, és figyelje meg az alkalmazás eredményét. Figyelje meg, hogy egyes lapkák és kiválasztások sikertelenek lesznek az adatbázis áttelepítése során.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A következő paranccsal ellenőrizheti a vész-helyreállítási kiszolgáló szerepkörét a teszt során:

```powershell
(Get-AzSqlDatabaseFailoverGroup -FailoverGroupName $failoverGroup `
    -ResourceGroupName $resourceGroup -ServerName $drServer).ReplicationRole
```

Feladatátvétel tesztelése:

1. A feladatátvételi csoport manuális feladatátvételének elindítása:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $resourceGroup `
    -ServerName $drServer -FailoverGroupName $failoverGroup
   ```

1. A feladatátvételi csoport visszaállítása az elsődleges kiszolgálóra:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $resourceGroup `
    -ServerName $server -FailoverGroupName $failoverGroup
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

A következő paranccsal ellenőrizheti a vész-helyreállítási kiszolgáló szerepkörét a teszt során:

```azurecli
az sql failover-group show --name $failoverGroup --resource-group $resourceGroup --server $drServer
```

Feladatátvétel tesztelése:

1. A feladatátvételi csoport manuális feladatátvételének elindítása:

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $drServer
   ```

1. A feladatátvételi csoport visszaállítása az elsődleges kiszolgálóra:

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   ```

* * *

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy adatbázist konfigurált Azure SQL Database és egy távoli régióba való feladatátvételre szolgáló alkalmazást, és tesztelt egy feladatátvételi tervet. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
>
> - Georeplikációs feladatátvételi csoport létrehozása
> - Java-alkalmazás futtatása SQL Database-adatbázis lekérdezéséhez
> - Feladatátvétel tesztelése

Folytassa a következő oktatóanyaggal, amely azt ismerteti, hogyan adható hozzá egy Azure SQL felügyelt példány egy példánya egy feladatátvételi csoporthoz:

> [!div class="nextstepaction"]
> [Azure SQL felügyelt példány példányának hozzáadása feladatátvételi csoporthoz](../managed-instance/failover-group-add-instance-tutorial.md)
