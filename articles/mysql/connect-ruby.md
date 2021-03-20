---
title: 'Gyors útmutató: kapcsolat a Ruby-Azure Database for MySQL használatával'
description: Ez a rövid útmutató számos Ruby-mintakódot biztosít, amelyekkel csatlakozhat a MySQL-hez készült Azure-adatbázishoz, illetve adatokat kérdezhet le róla.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.devlang: ruby
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: 4eba3fabee50e0011d5a63297c726a9647dd84c0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97831533"
---
# <a name="quickstart-use-ruby-to-connect-and-query-data-in-azure-database-for-mysql"></a>Rövid útmutató: a Ruby használatával csatlakozhat és lekérdezheti Azure Database for MySQL

Ez a rövid útmutató ismerteti, hogyan használható egy [Ruby](https://www.ruby-lang.org)-alkalmazás és a [mysql2](https://rubygems.org/gems/mysql2) gem a MySQL-hez készült Azure-adatbázishoz való csatlakozáshoz Windows, Ubuntu Linux és Mac platformról. Bemutatjuk, hogy az SQL-utasítások használatával hogyan kérdezhetők le, illeszthetők be, frissíthetők és törölhetők az adatok az adatbázisban. Ez a témakör azt feltételezi, hogy Ön a Ruby használata terén rendelkezik fejlesztési tapasztalatokkal, de a MySQL-hez készült Azure Database használatában még járatlan.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató az alábbi útmutatók valamelyikében létrehozott erőforrásokat használja kiindulópontként:
- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure Portal használatával](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure CLI használatával](./quickstart-create-mysql-server-database-using-azure-cli.md)

> [!IMPORTANT]
> Győződjön meg arról, hogy az IP-cím, amelyhez csatlakozik, a [Azure Portal](./howto-manage-firewall-using-portal.md) vagy az [Azure CLI](./howto-manage-firewall-using-cli.md) használatával adja hozzá a kiszolgáló tűzfalszabály-szabályait.

## <a name="install-ruby"></a>A Ruby telepítése
Telepítse a Rubyt, a Gemet és a MySQL2 könyvtárat a saját számítógépére.

### <a name="windows"></a>Windows
1. Töltse le és telepítse a [Ruby](https://rubyinstaller.org/downloads/) 2.3-as verzióját.
2. Nyisson meg egy új parancssort (cmd) a Start menüből.
3. Lépjen a Ruby-könyvtárba a 2.3-as verzióhoz. `cd c:\Ruby23-x64\bin`
4. A Ruby telepítésének ellenőrzéséhez futtassa a `ruby -v` parancsot a telepített verzió megtekintéséhez.
5. A Gem telepítésének ellenőrzéséhez futtassa a `gem -v` parancsot a telepített verzió megtekintéséhez.
6. A Gem használatával állítsa össze a Mysql2 modult a Rubyhoz a `gem install mysql2` parancs futtatásával.

### <a name="macos"></a>macOS
1. A következő parancs futtatásával telepítse a Rubyt a Homebrew használatával: `brew install ruby`. A további telepítési lehetőségekről a Ruby [telepítési dokumentációjából](https://www.ruby-lang.org/en/documentation/installation/#homebrew) tájékozódhat.
2. A Ruby telepítésének ellenőrzéséhez futtassa a `ruby -v` parancsot a telepített verzió megtekintéséhez.
3. A Gem telepítésének ellenőrzéséhez futtassa a `gem -v` parancsot a telepített verzió megtekintéséhez.
4. A Gem használatával állítsa össze a Mysql2 modult a Rubyhoz a `gem install mysql2` parancs futtatásával.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. A következő parancs futtatásával telepítse a Rubyt: `sudo apt-get install ruby-full`. A további telepítési lehetőségekről a Ruby [telepítési dokumentációjából](https://www.ruby-lang.org/en/documentation/installation/) tájékozódhat.
2. A Ruby telepítésének ellenőrzéséhez futtassa a `ruby -v` parancsot a telepített verzió megtekintéséhez.
3. Telepítse a legújabb Gem-frissítéseket a következő parancs futtatásával: `sudo gem update --system`.
4. A Gem telepítésének ellenőrzéséhez futtassa a `gem -v` parancsot a telepített verzió megtekintéséhez.
5. A következő parancs futtatásával telepítse a gcc, a make és az egyéb összeállítási eszközöket: `sudo apt-get install build-essential`.
6. Telepítse a MySQL-ügyfél fejlesztői könyvtárait a `sudo apt-get install libmysqlclient-dev` parancs futtatásával.
7. A Gem használatával állítsa össze a mysql2 modult a Rubyhoz a `sudo gem install mysql2` parancs futtatásával.

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése
Kérje le a MySQL-hez készült Azure Database-hez való csatlakozáshoz szükséges kapcsolatadatokat. Szüksége lesz a teljes kiszolgálónévre és a bejelentkezési hitelesítő adatokra.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/).
2. Az Azure Portal bal oldali menüjében kattintson a **Minden erőforrás** lehetőségre, és keressen rá a létrehozott kiszolgálóra (például **mydemoserver**).
3. Kattintson a kiszolgálónévre.
4. A kiszolgáló **Áttekintés** paneléről jegyezze fel a **Kiszolgálónevet** és a **Kiszolgáló-rendszergazdai bejelentkezési nevet**. Ha elfelejti a jelszavát, ezen a panelen új jelszót is tud kérni.
 :::image type="content" source="./media/connect-ruby/1_server-overview-name-login.png" alt-text="A MySQL-hez készült Azure Database-kiszolgáló neve":::

## <a name="run-ruby-code"></a>Ruby-kód futtatása
1. Illessze be a Ruby-kódot az alábbi szakaszokból szövegfájlokba, majd mentse a fájlokat egy projektmappába .rb kiterjesztéssel (például a `C:\rubymysql\createtable.rb` vagy `/home/username/rubymysql/createtable.rb` elérési úton).
2. A kód futtatásához nyissa meg a parancssort vagy a Bash rendszerhéjat. Lépjen a projektmappára: `cd rubymysql`
3. Ezután írja be a Ruby-parancsot, majd a fájlnevet (`ruby createtable.rb`) az alkalmazás futtatásához.
4. Ha Windows rendszeren a Ruby-alkalmazás nincs a path környezeti változóban, előfordulhat, hogy a teljes elérési utat kell használnia a Node.js-alkalmazás elindításához, például: `"c:\Ruby23-x64\bin\ruby.exe" createtable.rb`

## <a name="connect-and-create-a-table"></a>Csatlakozás és tábla létrehozása
A következő kód használatával csatlakozhat, és létrehozhat egy táblát a **CREATE TABLE** SQL-utasítással, majd az **INSERT INTO** SQL-utasításokkal sorokat adhat hozzá a táblához.

A kód egy mysql2:: Client osztályt használ a MySQL-kiszolgálóhoz való kapcsolódáshoz. Ezután meghívja a metódust ```query()``` a drop, a CREATE TABLE és a INSERT INTO parancs futtatásához. Végül hívja meg a ```close()``` -t, és zárja be a kapcsolódást a megszakítás előtt.

Cserélje le a `host`, `database`, `username`, és `password` sztringeket a saját értékekre.
```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Drop previous table of same name if one exists
    client.query('DROP TABLE IF EXISTS inventory;')
    puts 'Finished dropping table (if existed).'

    # Drop previous table of same name if one exists.
    client.query('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);')
    puts 'Finished creating table.'

    # Insert some data into table.
    client.query("INSERT INTO inventory VALUES(1, 'banana', 150)")
    client.query("INSERT INTO inventory VALUES(2, 'orange', 154)")
    client.query("INSERT INTO inventory VALUES(3, 'apple', 100)")
    puts 'Inserted 3 rows of data.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="read-data"></a>Adatok olvasása
A következő kóddal csatlakozhat, és beolvashatja az adatokat a **SELECT** SQL-utasítással.

A kód egy mysql2:: Client osztályt használ a Azure Database for MySQLhoz való kapcsolódáshoz a ```new()``` metódussal. Ezután meghívja a metódust ```query()``` a Select parancsok futtatásához. Ezután meghívja a metódust ```close()``` , hogy lezárja a kapcsolódást a megszakítás előtt.

Cserélje le a `host`, `database`, `username`, és `password` sztringeket a saját értékekre.

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Read data
    resultSet = client.query('SELECT * from inventory;')
    resultSet.each do |row|
        puts 'Data row = (%s, %s, %s)' % [row['id'], row['name'], row['quantity']]
    end
    puts 'Read ' + resultSet.count.to_s + ' row(s).'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="update-data"></a>Adatok frissítése
Az alábbi kód használatával csatlakozhat és végezheti el az adatok módosítását egy **UPDATE** SQL-utasítás segítségével.

A kód a [mysql2::client](https://rubygems.org/gems/mysql2-client-general_log) class .new() metódust használja a MySQL-hez készült Azure-adatbázishoz való csatlakozáshoz. Ezután meghívja a metódust ```query()```  a frissítési parancsok futtatásához. Ezután meghívja a metódust ```close()``` , hogy lezárja a kapcsolódást a megszakítás előtt.

Cserélje le a `host`, `database`, `username`, és `password` sztringeket a saját értékekre.

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Update data
   client.query('UPDATE inventory SET quantity = %d WHERE name = %s;' % [200, '\'banana\''])
   puts 'Updated 1 row of data.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```


## <a name="delete-data"></a>Adat törlése
A következő kóddal csatlakozhat, és beolvashatja az adatokat a **DELETE** SQL-utasítással.

A kód egy [mysql2:: Client](https://rubygems.org/gems/mysql2/) osztályt használ a MySQL-kiszolgálóhoz való kapcsolódáshoz, futtatja a DELETE parancsot, majd lezárta a kapcsolatot a kiszolgálóval.

Cserélje le a `host`, `database`, `username`, és `password` sztringeket a saját értékekre.

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Delete data
    resultSet = client.query('DELETE FROM inventory WHERE name = %s;' % ['\'orange\''])
    puts 'Deleted 1 row.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
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
> [Adatbázis migrálása exportálással és importálással](./concepts-migrate-import-export.md) <br/>

> [!div class="nextstepaction"]
> [További információ az MySQL2-ügyfélről](https://rubygems.org/gems/mysql2-client-general_log) <br/>

