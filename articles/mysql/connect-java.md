---
title: 'Rövid útmutató: a Java és a JDBC használata Azure Database for MySQL'
description: Ismerje meg, hogyan használható a Java és a JDBC Azure Database for MySQL adatbázissal.
author: jdubois
ms.author: judubois
ms.service: mysql
ms.custom: mvc, devcenter, devx-track-azurecli
ms.topic: quickstart
ms.devlang: java
ms.date: 08/17/2020
ms.openlocfilehash: 457f7e07391c647d2ab0e7d78197086f6f5e2cf7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96187782"
---
# <a name="quickstart-use-java-and-jdbc-with-azure-database-for-mysql"></a>Rövid útmutató: a Java és a JDBC használata Azure Database for MySQL

Ez a témakör bemutatja, hogyan hozhat létre egy olyan minta alkalmazást, amely a javát és a [JDBC](https://en.wikipedia.org/wiki/Java_Database_Connectivity) -t használja [Azure Database for MySQLban](./index.yml)található információk tárolására és lekérésére.

A JDBC a szabványos Java API a hagyományos kapcsolati adatbázisokhoz való kapcsolódáshoz.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-fiók. Ha még nem rendelkezik ilyennel, [kérjen meg egy ingyenes próbaverziót](https://azure.microsoft.com/free/).
- [Azure Cloud Shell](../cloud-shell/quickstart.md) vagy az [Azure CLI](/cli/azure/install-azure-cli). Javasoljuk, hogy Azure Cloud Shell, hogy automatikusan bejelentkezzen, és hozzáférhessen minden szükséges eszközhöz.
- Támogatott [Java Development Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), 8-as verzió (Azure Cloud Shell).
- Az [Apache Maven](https://maven.apache.org/) Build eszköz.

## <a name="prepare-the-working-environment"></a>A munkakörnyezet előkészítése

Környezeti változókat fogunk használni a gépelési hibák korlátozása érdekében, és egyszerűbbé tesszük a következő konfiguráció testreszabását az adott igényeknek megfelelően.

Állítsa be ezeket a környezeti változókat a következő parancsok használatával:

```bash
AZ_RESOURCE_GROUP=database-workshop
AZ_DATABASE_NAME=<YOUR_DATABASE_NAME>
AZ_LOCATION=<YOUR_AZURE_REGION>
AZ_MYSQL_USERNAME=demo
AZ_MYSQL_PASSWORD=<YOUR_MYSQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

A helyőrzőket írja felül a következő értékekkel, amelyeket a cikk teljes további részében használni fogunk:

- `<YOUR_DATABASE_NAME>`: A MySQL-kiszolgáló neve. Az Azure-on belül egyedinek kell lennie.
- `<YOUR_AZURE_REGION>`: A használni kívánt Azure-régió. Használhatja az alapértelmezett `eastus` értéket, de ajánlott az Ön lakóhelyéhez közelebbi régiót konfigurálni. Az elérhető régiók teljes listáját a beírásával érheti el `az account list-locations` .
- `<YOUR_MYSQL_PASSWORD>`: A MySQL adatbázis-kiszolgáló jelszava. A jelszónak legalább nyolc karakterből kell állnia. A karakterek között az alábbi kategóriákból háromnak kell szerepelnie: Az angol ábécé nagybetűi, az angol ábécé kisbetűi, számok (0-9) és egyéb karakterek (!, $, #, % stb.).
- `<YOUR_LOCAL_IP_ADDRESS>`: A helyi számítógép IP-címe, amelyből a Java-alkalmazást fogja futtatni. Az egyik kényelmes módszer, ha úgy találja, hogy a böngészőt a [whatismyip.Akamai.com](http://whatismyip.akamai.com/)irányítsa.

Ez után hozzon létre egy erőforráscsoportot:

```azurecli
az group create \
    --name $AZ_RESOURCE_GROUP \
    --location $AZ_LOCATION \
  	| jq
```

> [!NOTE]
> Itt az [Azure Cloud Shellben](https://shell.azure.com/) alapértelmezés szerint telepített `jq` segédprogramot használjuk a JSON-adatok olvashatóbb megjelenítésére.
> Ha Önnek nem tetszik ez a segédprogram, nyugodtan eltávolíthatja az összes parancs `| jq` részét.

## <a name="create-an-azure-database-for-mysql-instance"></a>Azure Database for MySQL-példány létrehozása

Először a felügyelt MySQL-kiszolgálót hozzuk létre.

> [!NOTE]
> Részletesebb információkat talál a MySQL-kiszolgálók létrehozásáról a következő cikkben: [Azure Database for MySQL-kiszolgáló létrehozása az Azure Portal használatával](./quickstart-create-mysql-server-database-using-azure-portal.md).

A [Azure Cloud Shell](https://shell.azure.com/)futtassa a következő parancsfájlt:

```azurecli
az mysql server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --sku-name B_Gen5_1 \
    --storage-size 5120 \
    --admin-user $AZ_MYSQL_USERNAME \
    --admin-password $AZ_MYSQL_PASSWORD \
  	| jq
```

Ez a parancs egy kis MySQL-kiszolgálót hoz létre.

### <a name="configure-a-firewall-rule-for-your-mysql-server"></a>Tűzfalszabály konfigurálása a MySQL-kiszolgálóhoz

A Azure Database for MySQL példányok alapértelmezés szerint biztonságosak. Tűzfallal rendelkezik, amely semmilyen bejövő kapcsolatot sem engedélyez. Az adatbázis használatához olyan tűzfalszabály hozzáadására van szükség, amely lehetővé teszi a helyi IP-cím számára az adatbázis-kiszolgáló elérését.

Mivel a cikk elején konfigurálta a helyi IP-címet, a kiszolgáló tűzfalát a következő parancs futtatásával nyithatja meg:

```azurecli
az mysql server firewall-rule create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME-database-allow-local-ip \
    --server $AZ_DATABASE_NAME \
    --start-ip-address $AZ_LOCAL_IP_ADDRESS \
    --end-ip-address $AZ_LOCAL_IP_ADDRESS \
  	| jq
```

### <a name="configure-a-mysql-database"></a>MySQL-adatbázis konfigurálása

A korábban létrehozott MySQL-kiszolgáló üres. Nincs olyan adatbázisa, amelyet a Java-alkalmazással használhat. Hozzon létre egy új, `demo` nevű adatbázist:

```azurecli
az mysql db create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name demo \
    --server-name $AZ_DATABASE_NAME \
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
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.20</version>
        </dependency>
    </dependencies>
</project>
```

Ez a fájl egy [Apache Maven](https://maven.apache.org/) , amely a következő használatára konfigurálja a projektet:

- Java 8
- A Java legújabb MySQL-illesztőprogramja

### <a name="prepare-a-configuration-file-to-connect-to-azure-database-for-mysql"></a>Konfigurációs fájl előkészítése Azure Database for MySQLhoz való csatlakozáshoz

Hozzon létre egy *src/Main/Resources/Application. properties* fájlt, és adja hozzá a következőket:

```properties
url=jdbc:mysql://$AZ_DATABASE_NAME.mysql.database.azure.com:3306/demo?serverTimezone=UTC
user=demo@$AZ_DATABASE_NAME
password=$AZ_MYSQL_PASSWORD
```

- Cserélje le a két `$AZ_DATABASE_NAME` változót a cikk elején konfigurált értékre.
- Cserélje le a `$AZ_MYSQL_PASSWORD` változót a cikk elején konfigurált értékre.

> [!NOTE]
> A `url` konfigurációs tulajdonsághoz fűzött `?serverTimezone=UTC` érték a JDBC-illesztőprogramnak adja meg, hogy az UTC (egyezményes világidő) dátumformátumot használja az adatbázishoz való csatlakozáskor. Ellenkező esetben a Java-kiszolgáló az adatbázisétól eltérő dátumformátumot használna, ez pedig hibát eredményezne.

### <a name="create-an-sql-file-to-generate-the-database-schema"></a>SQL-fájl létrehozása az adatbázis-séma létrehozásához

Egy adatbázis-séma létrehozásához a *src/Main `schema.sql` /Resources* /file-t fogjuk használni. Hozza létre a fájlt a következő tartalommal:

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
```

## <a name="code-the-application"></a>Az alkalmazás kódolása

### <a name="connect-to-the-database"></a>Csatlakozás az adatbázishoz

Ezután adja hozzá a Java-kódot, amely a JDBC-t fogja használni a MySQL-kiszolgálóról származó adatok tárolására és lekérésére.

Hozzon létre egy *src/Main/Java/DemoApplication. Java* fájlt, amely a következőket tartalmazza:

```java
package com.example.demo;

import com.mysql.cj.jdbc.AbandonedConnectionCleanupThread;

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
        AbandonedConnectionCleanupThread.uncheckedShutdown();
    }
}
```

Ez a Java-kód az *Application. properties* és a korábban létrehozott *Schema. SQL* fájlokat fogja használni, hogy csatlakozhasson a MySQL-kiszolgálóhoz, és hozzon létre egy sémát, amely az adattárolást fogja tárolni.

Ebben a fájlban láthatja, hogy az adatok beszúrására, olvasására, frissítésére és törlésére szolgáló metódusok megjegyzései: ezeket a metódusokat a cikk további részében fogjuk felírni, és egy másik után törölheti őket.

> [!NOTE]
> Az adatbázis hitelesítő adatait az *Application. properties* fájl *felhasználói* és *jelszó* tulajdonságaiban tárolja a rendszer. Ezeket a hitelesítő adatokat a rendszer a végrehajtáskor használja `DriverManager.getConnection(properties.getProperty("url"), properties);` , mivel a tulajdonságok fájlját argumentumként adja át a rendszer.

> [!NOTE]
> A `AbandonedConnectionCleanupThread.uncheckedShutdown();` végén található sor egy MySQL-illesztőprogram-specifikus parancs, amely egy belső szálat semmisít meg az alkalmazás leállításakor.
> Nyugodtan figyelmen kívül hagyható. 

Ezt a fő osztályt a kedvenc eszközével is végrehajthatja:

- Az IDE használatával kattintson a jobb gombbal a *DemoApplication* osztályra, és hajtsa végre.
- A Maven használatával futtathatja az alkalmazást az alábbiak végrehajtásával: `mvn exec:java -Dexec.mainClass="com.example.demo.DemoApplication"` .

Az alkalmazásnak kapcsolódnia kell a Azure Database for MySQLhoz, létre kell hoznia egy adatbázis-sémát, majd be kell kapcsolnia a kapcsolatot, ahogy az a konzol naplófájljaiban látható:

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

### <a name="insert-data-into-azure-database-for-mysql"></a>Adatbeszúrás Azure Database for MySQLba

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

### <a name="reading-data-from-azure-database-for-mysql"></a>Adatok olvasása a Azure Database for MySQLból

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

### <a name="updating-data-in-azure-database-for-mysql"></a>Az Azure Database for MySQL-beli Adatfrissítés

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

### <a name="deleting-data-in-azure-database-for-mysql"></a>Az Azure Database for MySQLban lévő adattörlés

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

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Gratulálunk! Létrehozott egy Java-alkalmazást, amely a JDBC-t használja a Azure Database for MySQL adatok tárolására és lekérésére.

Az ebben a rövid útmutatóban használt összes erőforrás törléséhez törölje az erőforráscsoportot a következő parancs használatával:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [MySQL-adatbázis migrálása a MySQL-hez készült Azure Database-be memóriakép és visszaállítás használatával](concepts-migrate-dump-restore.md)
