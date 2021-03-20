---
title: 'Gyors útmutató: kapcsolódjon a Ruby-Azure Database for PostgreSQL – egyetlen kiszolgálóval'
description: Ez a rövid útmutató egy Ruby-kódrészletet tartalmaz, amellyel összekapcsolhatók és beolvashatók az Azure Database for PostgreSQL-kiszolgálóról származó adatok.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc
ms.devlang: ruby
ms.topic: quickstart
ms.date: 5/6/2019
ms.openlocfilehash: 20e45454284af230da74896d5b3f5e9da676dbb4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97831703"
---
# <a name="quickstart-use-ruby-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Rövid útmutató: a Ruby használata az Azure Database for PostgreSQL-kiszolgálóval való kapcsolódáshoz és az adatlekérdezéshez

Ebben a gyors útmutatóban azt szemléltetjük, hogy miként lehet egy [Ruby](https://www.ruby-lang.org)-alkalmazás használatával csatlakozni a PostgreSQL-hez készült Azure Database-hez. Bemutatjuk, hogy az SQL-utasítások használatával hogyan kérdezhetők le, illeszthetők be, frissíthetők és törölhetők az adatok az adatbázisban. A jelen cikkben ismertetett lépések feltételezik, hogy Ön rendelkezik fejlesztési tapasztalatokkal a Ruby használatával kapcsolatban, az Azure Database for PostgreSQL használatában pedig még járatlan.

## <a name="prerequisites"></a>Előfeltételek
A rövid útmutató az alábbi útmutatók valamelyikében létrehozott erőforrásokat használja kiindulópontként:
- [DB létrehozása – portál](quickstart-create-server-database-portal.md)
- [DB létrehozása – Azure CLI](quickstart-create-server-database-azure-cli.md)

A következőket is telepítenie kell:
- [Ruby](https://www.ruby-lang.org/en/downloads/)
- [Ruby PG](https://rubygems.org/gems/pg/), a Rubyhoz tartozó PostgreSQL-modul

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése
Kérje le a PostgreSQL-hez készült Azure-adatbázishoz való csatlakozáshoz szükséges kapcsolatadatokat. Szüksége lesz a teljes kiszolgálónévre és a bejelentkezési hitelesítő adatokra.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/).
2. Az Azure Portal bal oldali menüjében kattintson a **Minden erőforrás** lehetőségre, és keressen rá a létrehozott kiszolgálóra (például **mydemoserver**).
3. Kattintson a kiszolgálónévre.
4. A kiszolgáló **Áttekintés** paneléről jegyezze fel a **Kiszolgálónevet** és a **Kiszolgáló-rendszergazdai bejelentkezési nevet**. Ha elfelejti a jelszavát, ezen a panelen új jelszót is tud kérni.
 :::image type="content" source="./media/connect-ruby/1-connection-string.png" alt-text="Azure Database for PostgreSQL-kiszolgáló neve":::

> [!NOTE]
> Az `@` Azure postgres-beli felhasználónévben szereplő szimbólum az összes kapcsolatban lévő karakterláncnak megfelelően lett kódolva `%40` .

## <a name="connect-and-create-a-table"></a>Csatlakozás és tábla létrehozása
A következő kód segítségével csatlakozzon, és hozzon létre egy táblát a **CREATE TABLE** SQL-utasítással, majd az **INSERT INTO** SQL-utasítással adjon hozzá sorokat a táblához.

A kód egy olyan objektumot használ, amely ```PG::Connection``` létrehoz egy konstruktort ```new``` Azure Database for PostgreSQLhoz való kapcsolódáshoz. Ezután meghívja a metódust ```exec()``` a drop, a CREATE TABLE és a INSERT INTO parancs futtatásához. A kód a osztály használatával ellenőrzi a hibákat ```PG::Error``` . Ezután meghívja a metódust ```close()``` , hogy lezárja a kapcsolódást a megszakítás előtt. Ezen osztályokkal és módszerekkel kapcsolatos további információkért tekintse meg a Ruby PG dokumentációját.

Cserélje le a `host`, `database`, `user`, és `password` sztringeket a saját értékekre.


```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database'

    # Drop previous table of same name if one exists
    connection.exec('DROP TABLE IF EXISTS inventory;')
    puts 'Finished dropping table (if existed).'

    # Drop previous table of same name if one exists.
    connection.exec('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);')
    puts 'Finished creating table.'

    # Insert some data into table.
    connection.exec("INSERT INTO inventory VALUES(1, 'banana', 150)")
    connection.exec("INSERT INTO inventory VALUES(2, 'orange', 154)")
    connection.exec("INSERT INTO inventory VALUES(3, 'apple', 100)")
    puts 'Inserted 3 rows of data.'

rescue PG::Error => e
    puts e.message

ensure
    connection.close if connection
end
```

## <a name="read-data"></a>Adatok olvasása
Az alábbi kód használatával csatlakozhat és végezheti el az adatok olvasását **SELECT** SQL-utasítás segítségével.

A kód egy olyan objektumot használ, amely  ```PG::Connection``` létrehoz egy konstruktort ```new``` Azure Database for PostgreSQLhoz való kapcsolódáshoz. Ezt követően a metódus meghívásával ```exec()``` futtatja a Select parancsot, és megtartja az eredményeket egy eredményhalmaz alapján. Az eredményhalmaz gyűjtése többször is végrehajtódik a `resultSet.each do` ciklus használatával, megőrizve az aktuális sor értékeit a `row` változóban. A kód a osztály használatával ellenőrzi a hibákat ```PG::Error``` . Ezután meghívja a metódust ```close()``` , hogy lezárja a kapcsolódást a megszakítás előtt. Ezen osztályokkal és módszerekkel kapcsolatos további információkért tekintse meg a Ruby PG dokumentációját.

Cserélje le a `host`, `database`, `user`, és `password` sztringeket a saját értékekre.

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :database => dbname, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    resultSet = connection.exec('SELECT * from inventory;')
    resultSet.each do |row|
        puts 'Data row = (%s, %s, %s)' % [row['id'], row['name'], row['quantity']]
    end

rescue PG::Error => e
    puts e.message

ensure
    connection.close if connection
end
```

## <a name="update-data"></a>Adatok frissítése
A következő kód használatával csatlakozhat, és frissítheti az adatokat az **UPDATE** SQL-utasítással.

A kód egy olyan objektumot használ, amely  ```PG::Connection``` létrehoz egy konstruktort ```new``` Azure Database for PostgreSQLhoz való kapcsolódáshoz. Ezután meghívja a metódust ```exec()``` az Update parancs futtatásához. A kód a osztály használatával ellenőrzi a hibákat ```PG::Error``` . Ezután meghívja a metódust ```close()``` , hogy lezárja a kapcsolódást a megszakítás előtt. Ezen osztályokkal és módszerekkel kapcsolatos további információkért tekintse meg a [Ruby PG dokumentációját](https://rubygems.org/gems/pg) .

Cserélje le a `host`, `database`, `user`, és `password` sztringeket a saját értékekre.

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    # Modify some data in table.
    connection.exec('UPDATE inventory SET quantity = %d WHERE name = %s;' % [200, '\'banana\''])
    puts 'Updated 1 row of data.'

rescue PG::Error => e
    puts e.message

ensure
    connection.close if connection
end
```


## <a name="delete-data"></a>Adat törlése
A következő kód használatával csatlakozhat, és beolvashatja az adatokat a **DELETE** SQL-utasítással.

A kód egy olyan objektumot használ, amely  ```PG::Connection``` létrehoz egy konstruktort ```new``` Azure Database for PostgreSQLhoz való kapcsolódáshoz. Ezután meghívja a metódust ```exec()``` az Update parancs futtatásához. A kód a osztály használatával ellenőrzi a hibákat ```PG::Error``` . Ezután meghívja a metódust ```close()``` , hogy lezárja a kapcsolódást a megszakítás előtt.

Cserélje le a `host`, `database`, `user`, és `password` sztringeket a saját értékekre.

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    # Modify some data in table.
    connection.exec('DELETE FROM inventory WHERE name = %s;' % ['\'orange\''])
    puts 'Deleted 1 row of data.'

rescue PG::Error => e
    puts e.message

ensure
    connection.close if connection
end
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a rövid útmutatóban használt összes erőforrás törléséhez törölje az erőforráscsoportot a következő parancs használatával:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálással és importálással](./howto-migrate-using-export-and-import.md) <br/>
> [!div class="nextstepaction"]
> [A Ruby PG dokumentációja](https://rubygems.org/gems/pg)
