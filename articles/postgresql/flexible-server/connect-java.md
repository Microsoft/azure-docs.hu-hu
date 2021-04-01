---
title: 'Rövid útmutató: a Java és a JDBC használata Azure Database for PostgreSQL rugalmas kiszolgálóval'
description: Ebből a rövid útmutatóból megtudhatja, hogyan használható a Java és a JDBC Azure Database for PostgreSQL rugalmas kiszolgálóval.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devcenter, devx-track-azurecli
ms.topic: quickstart
ms.devlang: java
ms.date: 01/16/2021
ms.openlocfilehash: 6c61e26ca858c5eb12ffe0219993f7477b0e8133
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98606110"
---
# <a name="quickstart-use-java-and-jdbc-with-azure-database-for-postgresql-flexible-server"></a>Rövid útmutató: a Java és a JDBC használata Azure Database for PostgreSQL rugalmas kiszolgálóval

Ez a témakör bemutatja, hogyan hozhat létre egy olyan minta alkalmazást, amely a javát és a [JDBC](https://en.wikipedia.org/wiki/Java_Database_Connectivity) -t használja [Azure Database for PostgreSQL rugalmas kiszolgáló](./index.yml)adatainak tárolására és lekérésére.

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
AZ_POSTGRESQL_USERNAME=demo
AZ_POSTGRESQL_PASSWORD=<YOUR_POSTGRESQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

A helyőrzőket írja felül a következő értékekkel, amelyeket a cikk teljes további részében használni fogunk:

- `<YOUR_DATABASE_NAME>`: A PostgreSQL-kiszolgáló neve. Az Azure-on belül egyedinek kell lennie.
- `<YOUR_AZURE_REGION>`: A használni kívánt Azure-régió. Használhatja az alapértelmezett `eastus` értéket, de ajánlott az Ön lakóhelyéhez közelebbi régiót konfigurálni. Az elérhető régiók teljes listáját a beírásával érheti el `az account list-locations` .
- `<YOUR_POSTGRESQL_PASSWORD>`: A PostgreSQL-adatbázis kiszolgálójának jelszava. A jelszónak legalább nyolc karakterből kell állnia. A karakterek között az alábbi kategóriákból háromnak kell szerepelnie: Az angol ábécé nagybetűi, az angol ábécé kisbetűi, számok (0-9) és egyéb karakterek (!, $, #, % stb.).
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

## <a name="create-an-azure-database-for-postgresql-instance"></a>Azure Database for PostgreSQL példány létrehozása

A létrehozott első dolog egy felügyelt PostgreSQL-kiszolgáló.

> [!NOTE]
> A PostgreSQL-kiszolgálók létrehozásával kapcsolatos részletes információkat [a Azure Portal használatával Azure Database for PostgreSQL kiszolgáló létrehozása](./quickstart-create-server-portal.md)című témakörben olvashat.

A [Azure Cloud Shell](https://shell.azure.com/)futtassa a következő parancsot:

```azurecli
az postgres flexible-server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --sku-name Standard_B1s \
    --storage-size 5120 \
    --admin-user $AZ_POSTGRESQL_USERNAME \
    --admin-password $AZ_POSTGRESQL_PASSWORD \
    --public-access $AZ_LOCAL_IP_ADDRESS
  	| jq
```

[Problémákba ütközik? Tudassa velünk.](https://github.com/MicrosoftDocs/azure-docs/issues)

### <a name="configure-a-postgresql-database"></a>PostgreSQL-adatbázis konfigurálása

A korábban létrehozott PostgreSQL-kiszolgáló rendelkezik egy üres adatbázis- **postgres** . Ehhez a rövid útmutatóhoz hozzon létre egy új adatbázist, amelyet a `demo` következő lépések követnek:

1. Telepítse a [psql](https://www.postgresql.org/docs/current/static/app-psql.html) helyi példányát egy Azure PostgreSQL-kiszolgálóhoz való kapcsolódáshoz. 
2. Futtassa az alábbi psql-parancsot a PostgreSQL-kiszolgálón található **postgres** -adatbázishoz való kapcsolódáshoz

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=postgres
   ```

   Példa: 
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

3. Hozzon létre egy **bemutató** nevű új adatbázist a parancssorba a következő parancs beírásával:

    ```bash
    CREATE DATABASE demo;
    ```

[Problémákba ütközik? Tudassa velünk.](https://github.com/MicrosoftDocs/azure-docs/issues)

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
            <groupId>org.postgresql</groupId>
            <artifactId>postgresql</artifactId>
            <version>42.2.12</version>
        </dependency>
    </dependencies>
</project>
```

Ez a fájl egy [Apache Maven](https://maven.apache.org/) , amely a következő használatára konfigurálja a projektet:

- Java 8
- A legújabb PostgreSQL-illesztőprogram Javához

### <a name="prepare-a-configuration-file-to-connect-to-azure-database-for-postgresql"></a>Konfigurációs fájl előkészítése Azure Database for PostgreSQLhoz való csatlakozáshoz

Hozzon létre egy *src/Main/Resources/Application. properties* fájlt, és adja hozzá a következőket:

```properties
url=jdbc:postgresql://$AZ_DATABASE_NAME.postgres.database.azure.com:5432/demo?ssl=true&sslmode=require
user=demo
password=$AZ_POSTGRESQL_PASSWORD
```

- Cserélje le a két `$AZ_DATABASE_NAME` változót a cikk elején konfigurált értékre.
- Cserélje le a `$AZ_POSTGRESQL_PASSWORD` változót a cikk elején konfigurált értékre.

> [!NOTE]
> A `?ssl=true&sslmode=require` Configuration (konfiguráció) tulajdonsághoz való hozzáfűzésével `url` megtudhatja, hogy a JDBC illesztőprogram a TLS-t ([Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security)) használja az adatbázishoz való csatlakozáskor. A TLS-t a Azure Database for PostgreSQL használatával kötelező használni, és ez jó biztonsági gyakorlat.

### <a name="create-an-sql-file-to-generate-the-database-schema"></a>SQL-fájl létrehozása az adatbázis-séma létrehozásához

Egy adatbázis-séma létrehozásához a *src/Main `schema.sql` /Resources* /file-t fogjuk használni. Hozza létre a fájlt a következő tartalommal:

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
```

## <a name="code-the-application"></a>Az alkalmazás kódolása

### <a name="connect-to-the-database"></a>Csatlakozás az adatbázishoz

Ezután adja hozzá azt a Java-kódot, amely a JDBC-kiszolgálóról származó adatok tárolására és lekérésére használható.

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

[Problémákba ütközik? Tudassa velünk.](https://github.com/MicrosoftDocs/azure-docs/issues)

Ez a Java-kód az *Application. properties* és a korábban létrehozott *Schema. SQL* fájlokat fogja használni, hogy csatlakozhasson a PostgreSQL-kiszolgálóhoz, és hozzon létre egy sémát, amely az adattárolást fogja tárolni.

Ebben a fájlban láthatja, hogy az adatok beszúrására, olvasására, frissítésére és törlésére szolgáló metódusok megjegyzései: ezeket a metódusokat a cikk további részében fogjuk felírni, és egy másik után törölheti őket.

> [!NOTE]
> Az adatbázis hitelesítő adatait az *Application. properties* fájl *felhasználói* és *jelszó* tulajdonságaiban tárolja a rendszer. Ezeket a hitelesítő adatokat a rendszer a végrehajtáskor használja `DriverManager.getConnection(properties.getProperty("url"), properties);` , mivel a tulajdonságok fájlját argumentumként adja át a rendszer.

Ezt a fő osztályt a kedvenc eszközével is végrehajthatja:

- Az IDE használatával kattintson a jobb gombbal a *DemoApplication* osztályra, és hajtsa végre.
- A Maven használatával futtathatja az alkalmazást az alábbiak végrehajtásával: `mvn exec:java -Dexec.mainClass="com.example.demo.DemoApplication"` .

Az alkalmazásnak kapcsolódnia kell a Azure Database for PostgreSQLhoz, létre kell hoznia egy adatbázis-sémát, majd be kell kapcsolnia a kapcsolatot, ahogy az a konzol naplófájljaiban látható:

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

### <a name="insert-data-into-azure-database-for-postgresql"></a>Adatbeszúrás Azure Database for PostgreSQLba

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

### <a name="reading-data-from-azure-database-for-postgresql"></a>Adatok olvasása a Azure Database for PostgreSQLból

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

### <a name="updating-data-in-azure-database-for-postgresql"></a>Az Azure Database for PostgreSQL-beli Adatfrissítés

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

### <a name="deleting-data-in-azure-database-for-postgresql"></a>Az Azure Database for PostgreSQLban lévő adattörlés

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

Gratulálunk! Létrehozott egy Java-alkalmazást, amely a JDBC-t használja a Azure Database for PostgreSQL adatok tárolására és lekérésére.

Az ebben a rövid útmutatóban használt összes erőforrás törléséhez törölje az erőforráscsoportot a következő parancs használatával:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálással és importálással](../howto-migrate-using-export-and-import.md)
