---
title: A Java és a JDBC használata az Azure SQL Database-szel
description: Ismerje meg, hogyan használható a Java és a JDBC egy Azure SQL Database.
services: sql-database
author: jdubois
ms.author: judubois
ms.service: sql-database
ms.subservice: development
ms.topic: quickstart
ms.devlang: java
ms.date: 06/26/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: badf6b8887c356c2a7fc7308f6aa15f551e4bb67
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95972674"
---
# <a name="use-java-and-jdbc-with--azure-sql-database"></a>A Java és a JDBC használata Azure SQL Database

Ez a témakör bemutatja, hogyan hozhat létre egy olyan minta alkalmazást, amely a javát és a [JDBC](https://en.wikipedia.org/wiki/Java_Database_Connectivity) -t használja [Azure SQL Databaseban](/azure/sql-database/)található információk tárolására és lekérésére.

A JDBC a szabványos Java API a hagyományos kapcsolati adatbázisokhoz való kapcsolódáshoz.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-fiók. Ha még nem rendelkezik ilyennel, [kérjen meg egy ingyenes próbaverziót](https://azure.microsoft.com/free/).
- [Azure Cloud Shell](../../cloud-shell/quickstart.md) vagy az [Azure CLI](/cli/azure/install-azure-cli). Javasoljuk, hogy Azure Cloud Shell, hogy automatikusan bejelentkezzen, és hozzáférhessen minden szükséges eszközhöz.
- Támogatott [Java Development Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), 8-as verzió (Azure Cloud Shell).
- Az [Apache Maven](https://maven.apache.org/) Build eszköz.

## <a name="prepare-the-working-environment"></a>A munkakörnyezet előkészítése

Környezeti változókat fogunk használni a gépelési hibák korlátozása érdekében, és egyszerűbbé tesszük a következő konfiguráció testreszabását az adott igényeknek megfelelően.

Állítsa be ezeket a környezeti változókat a következő parancsok használatával:

```bash
AZ_RESOURCE_GROUP=database-workshop
AZ_DATABASE_NAME=<YOUR_DATABASE_NAME>
AZ_LOCATION=<YOUR_AZURE_REGION>
AZ_SQL_SERVER_USERNAME=demo
AZ_SQL_SERVER_PASSWORD=<YOUR_AZURE_SQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

A helyőrzőket írja felül a következő értékekkel, amelyeket a cikk teljes további részében használni fogunk:

- `<YOUR_DATABASE_NAME>`: A Azure SQL Database-kiszolgáló neve. Az Azure-on belül egyedinek kell lennie.
- `<YOUR_AZURE_REGION>`: A használni kívánt Azure-régió. Használhatja az alapértelmezett `eastus` értéket, de ajánlott az Ön lakóhelyéhez közelebbi régiót konfigurálni. Az elérhető régiók teljes listáját a beírásával érheti el `az account list-locations` .
- `<AZ_SQL_SERVER_PASSWORD>`: A Azure SQL Database-kiszolgáló jelszava. A jelszónak legalább nyolc karakterből kell állnia. A karakterek között az alábbi kategóriákból háromnak kell szerepelnie: Az angol ábécé nagybetűi, az angol ábécé kisbetűi, számok (0-9) és egyéb karakterek (!, $, #, % stb.).
- `<YOUR_LOCAL_IP_ADDRESS>`: A helyi számítógép IP-címe, amelyből a Java-alkalmazást fogja futtatni. Az egyik kényelmes módszer, ha úgy találja, hogy a böngészőt a [whatismyip.Akamai.com](http://whatismyip.akamai.com/)irányítsa.

Ezután hozzon létre egy erőforráscsoportot a következő parancs használatával:

```azurecli
az group create \
    --name $AZ_RESOURCE_GROUP \
    --location $AZ_LOCATION \
  	| jq
```

> [!NOTE]
> A `jq` segédprogramot a JSON-információk megjelenítésére használjuk, és olvashatóbbá teszik azokat. Ez a segédprogram alapértelmezés szerint telepítve van [Azure Cloud Shellon](https://shell.azure.com/). Ha Önnek nem tetszik ez a segédprogram, nyugodtan eltávolíthatja az összes parancs `| jq` részét.

## <a name="create-an-azure-sql-database-instance"></a>Azure SQL Database példány létrehozása

A létrehozott első dolog egy felügyelt Azure SQL Database-kiszolgáló.

> [!NOTE]
> A rövid útmutatóban Azure SQL Database kiszolgálók létrehozásával kapcsolatos részletes információkat a következő témakörben olvashatja [: Azure SQL Database önálló adatbázis létrehozása](./single-database-create-quickstart.md).

A [Azure Cloud Shell](https://shell.azure.com/)futtassa a következő parancsot:

```azurecli
az sql server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --admin-user $AZ_SQL_SERVER_USERNAME \
    --admin-password $AZ_SQL_SERVER_PASSWORD \
  	| jq
```

Ez a parancs létrehoz egy Azure SQL Database kiszolgálót.

### <a name="configure-a-firewall-rule-for-your-azure-sql-database-server"></a>Tűzfalszabály konfigurálása a Azure SQL Database-kiszolgálóhoz

A Azure SQL Database példányok alapértelmezés szerint biztonságosak. Tűzfallal rendelkezik, amely semmilyen bejövő kapcsolatot sem engedélyez. Az adatbázis használatához olyan tűzfalszabály hozzáadására van szükség, amely lehetővé teszi a helyi IP-cím számára az adatbázis-kiszolgáló elérését.

Mivel a cikk elején konfigurálta a helyi IP-címet, a következő parancs futtatásával nyithatja meg a kiszolgáló tűzfalát:

```azurecli
az sql server firewall-rule create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME-database-allow-local-ip \
    --server $AZ_DATABASE_NAME \
    --start-ip-address $AZ_LOCAL_IP_ADDRESS \
    --end-ip-address $AZ_LOCAL_IP_ADDRESS \
  	| jq
```

### <a name="configure-a-azure-sql-database"></a>Azure SQL Database konfigurálása

A korábban létrehozott Azure SQL Database-kiszolgáló üres. Nincs olyan adatbázisa, amelyet a Java-alkalmazással használhat. Hozzon létre egy nevű új adatbázist `demo` a következő parancs futtatásával:

```azurecli
az sql db create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name demo \
    --server $AZ_DATABASE_NAME \
  	| jq
```

### <a name="create-a-new-java-project"></a>Új Java-projekt létrehozása

Használja kedvenc IDE-t, hozzon létre egy új Java-projektet, és adjon hozzá egy `pom.xml` fájlt a gyökérkönyvtárában:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>demo</name>

    <properties>
        <java.version>1.8</java.version>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.microsoft.sqlserver</groupId>
            <artifactId>mssql-jdbc</artifactId>
            <version>7.4.1.jre8</version>
        </dependency>
    </dependencies>
</project>
```

Ez a fájl egy [Apache Maven](https://maven.apache.org/) , amely a következő használatára konfigurálja a projektet:

- Java 8
- A Java legújabb SQL Server-illesztőprogramja

### <a name="prepare-a-configuration-file-to-connect-to-azure-sql-database"></a>Konfigurációs fájl előkészítése az Azure SQL Database-hez való csatlakozáshoz

Hozzon létre egy *src/Main/Resources/Application. properties* fájlt, és adja hozzá a következőket:

```properties
url=jdbc:sqlserver://$AZ_DATABASE_NAME.database.windows.net:1433;database=demo;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
user=demo@$AZ_DATABASE_NAME
password=$AZ_SQL_SERVER_PASSWORD
```

- Cserélje le a két `$AZ_DATABASE_NAME` változót a cikk elején konfigurált értékre.
- Cserélje le a `$AZ_SQL_SERVER_PASSWORD` változót a cikk elején konfigurált értékre.

### <a name="create-an-sql-file-to-generate-the-database-schema"></a>SQL-fájl létrehozása az adatbázis-séma létrehozásához

Egy adatbázis-séma létrehozásához a *src/Main `schema.sql` /Resources* /file-t fogjuk használni. Hozza létre a fájlt a következő tartalommal:

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id INT PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BIT);
```

## <a name="code-the-application"></a>Az alkalmazás kódolása

### <a name="connect-to-the-database"></a>Csatlakozás az adatbázishoz

Ezután adja hozzá azt a Java-kódot, amely a JDBC-t használja az Azure SQL Database-ben tárolt adatok tárolására és lekérésére.

Hozzon létre egy *src/Main/Java/DemoApplication. Java* fájlt, amely a következőket tartalmazza:

```java
package com.example.demo;

import java.sql.*;
import java.util.*;
import java.util.logging.Logger;

public class DemoApplication {

    private static final Logger log;

    static {
        System.setProperty("java.util.logging.SimpleFormatter.format", "[%4$-7s] %5$s %n");
        log =Logger.getLogger(DemoApplication.class.getName());
    }

    public static void main(String[] args) throws Exception {
        log.info("Loading application properties");
        Properties properties = new Properties();
        properties.load(DemoApplication.class.getClassLoader().getResourceAsStream("application.properties"));

        log.info("Connecting to the database");
        Connection connection = DriverManager.getConnection(properties.getProperty("url"), properties);
        log.info("Database connection test: " + connection.getCatalog());

        log.info("Create database schema");
        Scanner scanner = new Scanner(DemoApplication.class.getClassLoader().getResourceAsStream("schema.sql"));
        Statement statement = connection.createStatement();
        while (scanner.hasNextLine()) {
            statement.execute(scanner.nextLine());
        }

        /*
        Todo todo = new Todo(1L, "configuration", "congratulations, you have set up JDBC correctly!", true);
        insertData(todo, connection);
        todo = readData(connection);
        todo.setDetails("congratulations, you have updated data!");
        updateData(todo, connection);
        deleteData(todo, connection);
        */

        log.info("Closing database connection");
        connection.close();
    }
}
```

Ez a Java-kód az *Application. properties* és a korábban létrehozott *Schema. SQL* fájlokat fogja használni, hogy csatlakozhasson a SQL Server-adatbázishoz, és hozzon létre egy sémát, amely az adattárolást fogja tárolni.

Ebben a fájlban láthatja, hogy az adatok beszúrására, olvasására, frissítésére és törlésére szolgáló metódusok megjegyzései: ezeket a metódusokat a cikk további részében fogjuk felírni, és egy másik után törölheti őket.

> [!NOTE]
> Az adatbázis hitelesítő adatait az *Application. properties* fájl *felhasználói* és *jelszó* tulajdonságaiban tárolja a rendszer. Ezeket a hitelesítő adatokat a rendszer a végrehajtáskor használja `DriverManager.getConnection(properties.getProperty("url"), properties);` , mivel a tulajdonságok fájlját argumentumként adja át a rendszer.

Ezt a fő osztályt a kedvenc eszközével is végrehajthatja:

- Az IDE használatával kattintson a jobb gombbal a *DemoApplication* osztályra, és hajtsa végre.
- A Maven használatával futtathatja az alkalmazást az alábbiak végrehajtásával: `mvn exec:java -Dexec.mainClass="com.example.demo.DemoApplication"` .

Az alkalmazásnak kapcsolódnia kell a Azure SQL Databasehoz, létre kell hoznia egy adatbázis-sémát, majd be kell kapcsolnia a kapcsolatot, ahogy az a konzol naplófájljaiban látható:

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Closing database connection 
```

### <a name="create-a-domain-class"></a>Tartományi osztály létrehozása

Hozzon létre egy új `Todo` Java-osztályt az osztály mellett, `DemoApplication` és adja hozzá a következő kódot:

```java
package com.example.demo;

public class Todo {

    private Long id;
    private String description;
    private String details;
    private boolean done;

    public Todo() {
    }

    public Todo(Long id, String description, String details, boolean done) {
        this.id = id;
        this.description = description;
        this.details = details;
        this.done = done;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }

    public String getDetails() {
        return details;
    }

    public void setDetails(String details) {
        this.details = details;
    }

    public boolean isDone() {
        return done;
    }

    public void setDone(boolean done) {
        this.done = done;
    }

    @Override
    public String toString() {
        return "Todo{" +
                "id=" + id +
                ", description='" + description + '\'' +
                ", details='" + details + '\'' +
                ", done=" + done +
                '}';
    }
}
```

Ez az osztály olyan tartományi modell, amely a `todo` *Schema. SQL* parancsfájl végrehajtásakor létrehozott táblán van leképezve.

### <a name="insert-data-into-azure-sql-database"></a>Adatbeszúrás az Azure SQL Database-be

Az *src/Main/Java/DemoApplication. Java* fájlban a Main metódust követően adja hozzá a következő metódust az adatbázisba való beszúráshoz:

```java
private static void insertData(Todo todo, Connection connection) throws SQLException {
    log.info("Insert data");
    PreparedStatement insertStatement = connection
            .prepareStatement("INSERT INTO todo (id, description, details, done) VALUES (?, ?, ?, ?);");

    insertStatement.setLong(1, todo.getId());
    insertStatement.setString(2, todo.getDescription());
    insertStatement.setString(3, todo.getDetails());
    insertStatement.setBoolean(4, todo.isDone());
    insertStatement.executeUpdate();
}
```

Mostantól megadhatja a következő két sor megjegyzését a `main` metódusban:

```java
Todo todo = new Todo(1L, "configuration", "congratulations, you have set up JDBC correctly!", true);
insertData(todo, connection);
```

A Main osztály végrehajtásához most a következő kimenetet kell létrehozni:

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Closing database connection
```

### <a name="reading-data-from-azure-sql-database"></a>Adatok beolvasása az Azure SQL Database-ből

Olvassa el a korábban beszúrt adatforrásokat, és ellenőrizze, hogy a kód megfelelően működik-e.

Az *src/Main/Java/DemoApplication. Java* fájlban a `insertData` metódus után adja hozzá a következő metódust az adatok az adatbázisból való beolvasásához:

```java
private static Todo readData(Connection connection) throws SQLException {
    log.info("Read data");
    PreparedStatement readStatement = connection.prepareStatement("SELECT * FROM todo;");
    ResultSet resultSet = readStatement.executeQuery();
    if (!resultSet.next()) {
        log.info("There is no data in the database!");
        return null;
    }
    Todo todo = new Todo();
    todo.setId(resultSet.getLong("id"));
    todo.setDescription(resultSet.getString("description"));
    todo.setDetails(resultSet.getString("details"));
    todo.setDone(resultSet.getBoolean("done"));
    log.info("Data read from the database: " + todo.toString());
    return todo;
}
```

Mostantól megadhatja a következő sor megjegyzését a `main` metódusban:

```java
todo = readData(connection);
```

A Main osztály végrehajtásához most a következő kimenetet kell létrehozni:

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true} 
[INFO   ] Closing database connection 
```

### <a name="updating-data-in-azure-sql-database"></a>Az Azure SQL Database-beli Adatfrissítés

Frissítjük a korábban beszúrt adathalmazokat.

Továbbra is a *src/Main/Java/DemoApplication. Java* fájlban, a `readData` metódus után adja hozzá a következő metódust az adatbázison belüli adatfrissítéshez:

```java
private static void updateData(Todo todo, Connection connection) throws SQLException {
    log.info("Update data");
    PreparedStatement updateStatement = connection
            .prepareStatement("UPDATE todo SET description = ?, details = ?, done = ? WHERE id = ?;");

    updateStatement.setString(1, todo.getDescription());
    updateStatement.setString(2, todo.getDetails());
    updateStatement.setBoolean(3, todo.isDone());
    updateStatement.setLong(4, todo.getId());
    updateStatement.executeUpdate();
    readData(connection);
}
```

Mostantól megadhatja a következő két sor megjegyzését a `main` metódusban:

```java
todo.setDetails("congratulations, you have updated data!");
updateData(todo, connection);
```

A Main osztály végrehajtásához most a következő kimenetet kell létrehozni:

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true} 
[INFO   ] Update data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have updated data!', done=true} 
[INFO   ] Closing database connection 
```

### <a name="deleting-data-in-azure-sql-database"></a>Az Azure SQL Database-ben tárolt adattörlés

Végül törölje az előzőleg beszúrt adathalmazt.

Továbbra is a *src/Main/Java/DemoApplication. Java* fájlban, a `updateData` metódus után adja hozzá a következő metódust az adatbázison belüli adattörléshez:

```java
private static void deleteData(Todo todo, Connection connection) throws SQLException {
    log.info("Delete data");
    PreparedStatement deleteStatement = connection.prepareStatement("DELETE FROM todo WHERE id = ?;");
    deleteStatement.setLong(1, todo.getId());
    deleteStatement.executeUpdate();
    readData(connection);
}
```

Mostantól megadhatja a következő sor megjegyzését a `main` metódusban:

```java
deleteData(todo, connection);
```

A Main osztály végrehajtásához most a következő kimenetet kell létrehozni:

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true} 
[INFO   ] Update data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have updated data!', done=true} 
[INFO   ] Delete data 
[INFO   ] Read data 
[INFO   ] There is no data in the database! 
[INFO   ] Closing database connection 
```

## <a name="conclusion-and-resources-clean-up"></a>Következtetések és erőforrások tisztítása

Gratulálunk! Létrehozott egy Java-alkalmazást, amely JDBC-t használ az Azure SQL Database-ben tárolt adatok tárolására és lekérésére.

Az ebben a rövid útmutatóban használt összes erőforrás törléséhez törölje az erőforráscsoportot a következő parancs használatával:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Következő lépések

- [Az első adatbázis megtervezése Azure SQL Database](design-first-database-tutorial.md)  
- [Microsoft JDBC-illesztőprogram SQL Serverhez](https://github.com/microsoft/mssql-jdbc)  
- [Hibák jelentése/kérdések felvetése](https://github.com/microsoft/mssql-jdbc/issues)
