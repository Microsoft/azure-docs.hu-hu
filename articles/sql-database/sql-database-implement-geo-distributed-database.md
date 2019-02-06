---
title: Földrajzilag elosztott Azure SQL adatbázis-megoldás implementálása |} A Microsoft Docs
description: Megtanulhatja, hogyan konfigurálja az Azure SQL database és az alkalmazás feladatátvételét egy replikált adatbázisra, és a feladatátvételi teszt érdekében.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 01/10/2019
ms.openlocfilehash: 11c1f34176e7852806464781e80d6dc0fd5345a4
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55750341"
---
# <a name="tutorial-implement-a-geo-distributed-database"></a>Oktatóanyag: Földrajzilag elosztott adatbázis implementálása

Konfigurálja egy Azure SQL database és az alkalmazás feladatátvételét egy távoli régióba, és tesztelheti a feladatátvételi tervet. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> - Hozzon létre egy [feladatátvételi csoport](sql-database-auto-failover-group.md)
> - Java-alkalmazások Azure SQL Database-adatbázis lekérdezéséhez
> - Feladatátvétel tesztelése

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy telepítette a következő elemek:

- [Azure PowerShell](/powershell/azureps-cmdlets-docs)
- Azure SQL Database-adatbázis. Egy használati létrehozása
  - [Portál](sql-database-single-database-get-started.md)
  - [Parancssori felület](sql-database-cli-samples.md)
  - [PowerShell](sql-database-powershell-samples.md)

  > [!NOTE]
  > Az oktatóanyag a *AdventureWorksLT* mintaadatbázissal.

- A Java és Maven, lásd: [alkalmazás készítése az SQL Server használatával](https://www.microsoft.com/sql-server/developer-get-started/), jelöljön ki **Java** , és válassza ki azt a környezetet, majd kövesse a lépéseket.

> [!IMPORTANT]
> Győződjön meg arról, a tűzfalszabályok beállítása annak a számítógépnek, amelyre hajt végre a lépéseket ebben az oktatóanyagban a nyilvános IP-címet használja. Adatbázisszintű tűzfal-szabályok automatikusan replikálja a másodlagos kiszolgálóra.
>
> További információ: [hozzon létre egy adatbázisszintű tűzfalszabályt](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) meghatározásához lásd a kiszolgálószintű tűzfalszabályt a számítógép a használt IP-cím vagy [kiszolgálószintű tűzfal létrehozása](sql-database-server-level-firewall-rule.md).  

## <a name="create-a-failover-group"></a>Hozzon létre egy feladatátvételi csoportot

Azure PowerShell használatával hozzon létre [feladatátvételi csoportok](sql-database-auto-failover-group.md) között egy meglévő Azure SQL server és a egy új Azure SQL server egy másik régióban. Ezután adja hozzá a mintaadatbázist a feladatátvételi csoporthoz.

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]

Hozzon létre egy feladatátvételi csoportot, futtassa a következő parancsfájlt:

   ```powershell
    # Set variables for your server and database
    $adminlogin = "<your admin>"
    $password = "<your password>"
    $myresourcegroupname = "<your resource group name>"
    $mylocation = "<your resource group location>"
    $myservername = "<your existing server name>"
    $mydatabasename = "<your database name>"
    $mydrlocation = "<your disaster recovery location>"
    $mydrservername = "<your disaster recovery server name>"
    $myfailovergroupname = "<your globally unique failover group name>"

    # Create a backup server in the failover region
    New-AzureRmSqlServer -ResourceGroupName $myresourcegroupname `
       -ServerName $mydrservername `
       -Location $mydrlocation `
       -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
          -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

    # Create a failover group between the servers
    New-AzureRMSqlDatabaseFailoverGroup `
       –ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -PartnerServerName $mydrservername  `
       –FailoverGroupName $myfailovergroupname `
       –FailoverPolicy Automatic `
       -GracePeriodWithDataLossHours 2

    # Add the database to the failover group
    Get-AzureRmSqlDatabase `
       -ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -DatabaseName $mydatabasename | `
     Add-AzureRmSqlDatabaseToFailoverGroup `
       -ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -FailoverGroupName $myfailovergroupname
   ```

Georeplikáció beállításokat is módosítható az Azure Portalon, majd az adatbázis kiválasztásával **beállítások** > **Georeplikációs**.

![Georeplikáció – beállítások](./media/sql-database-implement-geo-distributed-database/geo-replication.png)

## <a name="run-the-sample-project"></a>A minta-projekt futtatása

1. A konzolon hozzon létre egy Maven-projektet a következő parancsot:

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

1. Típus **Y** nyomja le az ENTER **Enter**.

1. Módosítsa a könyvtárakat az új projektbe.

   ```bash
   cd SqlDbSample
   ```

1. A kedvenc szerkesztőjében, nyissa meg a *pom.xml* fájlt a projektmappa fájllistájának.

1. Az SQL Server-függőség Microsoft JDBC-illesztőprogram hozzáadása a következő `dependency` szakaszban. A függőség kell beilleszteni a nagyobb `dependencies` szakaszban.

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

1. Adja meg a Java-verzió hozzáadásával a `properties` szakasz után a `dependencies` szakaszban:

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Jegyzékfájlok támogatásához hozzáadásával a `build` szakasz után a `properties` szakaszban:

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

1. Nyissa meg a *App.java* fájlt... \SqlDbSample\src\main\java\com\sqldbsamples, és cserélje ki annak tartalmát az alábbira:

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
                   (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " +
                   (selectData((highWaterMark + i))?"successful":"failed") + "\n");
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

1. Mentse és zárja be a *App.java* fájlt.

1. A parancskonzolban futtassa a következő parancsot:

   ```bash
   mvn package
   ```

1. Indítsa el az alkalmazást, körülbelül egy órán leállításáig manuálisan, így időt a feladatátvételi teszt futtatása esetén.

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

Futtassa az alábbi parancsfájlok a feladatátvétel szimulálhat, és figyelje meg az alkalmazás eredményeit. Figyelje meg, hogyan néhány szúr be, és kiválasztja az adatbázis-migrálás során sikertelen lesz.

A vészhelyreállítási kiszolgáló szerepe a következő paranccsal a teszt során is keresheti:

   ```powershell
   (Get-AzureRMSqlDatabaseFailoverGroup `
      -FailoverGroupName $myfailovergroupname `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername).ReplicationRole
   ```

A feladatátvétel tesztelése:

1. Indítsa el a feladatátvételi csoport manuális feladatátvételt:

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername `
      -FailoverGroupName $myfailovergroupname
   ```

1. Térhet vissza az elsődleges kiszolgálón a feladatátvételi csoport:

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -FailoverGroupName $myfailovergroupname
   ```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban konfigurálni egy Azure SQL database és az alkalmazás feladatátvételét egy távoli régióba, és tesztelni a feladatátvételi tervet. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> - Georeplikációs feladatátvételi csoport létrehozása
> - Java-alkalmazások Azure SQL Database-adatbázis lekérdezéséhez
> - Feladatátvétel tesztelése

Lépjen a következő oktatóanyagra a DMS használatával történő áttelepítés.

> [!div class="nextstepaction"]
> [SQL Server migrálása az Azure SQL database felügyelt példány DMS használatával](../dms/tutorial-sql-server-to-managed-instance.md)
