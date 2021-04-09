---
title: 'Gyors útmutató: kapcsolat a Python-Azure Database for PostgreSQL-rugalmas kiszolgáló használatával'
description: Ez a rövid útmutató számos Python-kódrészletet tartalmaz, amelyekkel csatlakozhat, és lekérdezheti Azure Database for PostgreSQL-rugalmas kiszolgáló adatait.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 09/22/2020
ms.openlocfilehash: 2bcf467eea9186cc57fb4c6c3d8964632b8fec03
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047711"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---flexible-server"></a>Rövid útmutató: a Python használata a Azure Database for PostgreSQL rugalmas kiszolgálóval való kapcsolódáshoz és az adatlekérdezéshez

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló előzetes verzióban érhető el

Ebben a rövid útmutatóban egy Azure Database for PostgreSQL-rugalmas kiszolgálóhoz csatlakozik a Python használatával. Ezután SQL-utasításokkal adatokat lehet lekérdezni, beszúrni, frissíteni és törölni az adatbázisban a Mac, Ubuntu Linux és Windows platformokról. 

Ez a cikk azt feltételezi, hogy már ismeri a fejlesztést a Python használatával, de most ismerkedik a Azure Database for PostgreSQL-rugalmas kiszolgálóval való munkavégzéssel.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Egy Azure Database for PostgreSQL rugalmas kiszolgáló. Rugalmas kiszolgáló létrehozásához tekintse meg a [Azure Database for PostgreSQL-rugalmas kiszolgáló létrehozása Azure Portal használatával](./quickstart-create-server-portal.md)című témakört.
* [Python](https://www.python.org/downloads/) 2,7 vagy 3.6 +.
* Legújabb [pip](https://pip.pypa.io/en/stable/installing/) -csomag telepítője.

## <a name="preparing-your-client-workstation"></a>Az ügyfél munkaállomásának előkészítése
- Ha a rugalmas kiszolgálót *privát hozzáféréssel (VNet-integrációval)* hozta létre, akkor a kiszolgálóval egy olyan erőforráshoz kell csatlakoznia, amely a kiszolgálóval megegyező VNet belül található. Létrehozhat egy virtuális gépet, és hozzáadhatja azt a rugalmas kiszolgálóval létrehozott VNet. Az [Azure CLI használatával a Azure Database for PostgreSQL rugalmas kiszolgálói virtuális hálózat létrehozása és kezelése](./how-to-manage-virtual-network-cli.md)című témakörben talál további információt.
- Ha a rugalmas kiszolgálót *nyilvános hozzáféréssel (engedélyezett IP-címekkel)* hozta létre, akkor a helyi IP-címét felveheti a kiszolgálón található tűzfalszabályok listájára. [Az Azure CLI használatával a Azure Database for PostgreSQL rugalmas kiszolgálói tűzfalszabályok létrehozása és kezelése](./how-to-manage-firewall-cli.md)című témakörben talál további információt.

## <a name="install-the-python-libraries-for-postgresql"></a>Python-kódtárak telepítése a PostgreSQL-hez
A [psycopg2](https://pypi.python.org/pypi/psycopg2/) modul lehetővé teszi a PostgreSQL-adatbázishoz való csatlakozást és azok lekérdezését, és Linux, MacOS vagy Windows [Wheel](https://pythonwheels.com/) csomagként érhető el. Telepítse a modul bináris verzióját, beleértve az összes függőséget. A `psycopg2` telepítéssel és a követelményekkel kapcsolatos további információkért lásd: [telepítés](http://initd.org/psycopg/docs/install.html). 

A telepítéséhez `psycopg2` Nyisson meg egy terminált vagy egy parancssort, és futtassa a parancsot `pip install psycopg2` .

## <a name="get-database-connection-information"></a>Adatbázis-kapcsolatok adatainak beolvasása
Azure Database for PostgreSQL-rugalmas kiszolgálóhoz való csatlakozáshoz a teljes kiszolgálónévre és a bejelentkezési hitelesítő adatokra van szükség. Ezt az információt a Azure Portalból kérheti le.

1. A [Azure Portal](https://portal.azure.com/)keresse meg és válassza ki a rugalmas kiszolgáló nevét. 
2. A kiszolgáló **Áttekintés** lapján másolja a teljes **kiszolgálónevet** és a **rendszergazdai felhasználónevet**. A teljes **kiszolgálónév** mindig a form *\<my-server-name> . postgres.database.Azure.com*.

   Szüksége lesz a rendszergazdai jelszavára is. Ha elfelejti, alaphelyzetbe állíthatja az áttekintés oldalról. 

   <!--![Azure Database for PostgreSQL server name](./media/connect-python/1-connection-string.png)-->

## <a name="how-to-run-the-python-examples"></a>A Python-példák futtatása

A cikkben szereplő kódok mindegyike esetében:

1. Hozzon létre egy új fájlt egy szövegszerkesztőben. 

1. Adja hozzá a példában szereplő kódot a fájlhoz. A kódban cserélje le a következőket:
   - `<server-name>` és a `<admin-username>` Azure Portalból másolt értékekkel.
   - `<admin-password>` a kiszolgáló jelszavával.
   - `<database-name>` a Azure Database for PostgreSQL rugalmas kiszolgálói adatbázisának nevével. A kiszolgáló létrehozásakor a rendszer automatikusan létrehozta a *postgres* nevű alapértelmezett adatbázist. Átnevezheti az adatbázist, vagy létrehozhat egy új adatbázist az SQL-parancsok használatával. 

1. Mentse a fájlt a Project mappájába *.* file kiterjesztéssel, például *postgres-INSERT.py*. Windows esetén győződjön meg arról, hogy az UTF-8 kódolás van kiválasztva a fájl mentésekor. 

1. A fájl futtatásához váltson egy parancssori felületen a Project mappára, és írja be például a következőt `python` : `python postgres-insert.py` .

## <a name="create-a-table-and-insert-data"></a>Tábla létrehozása és az adatbeszúrás
A következő kódrészlet a [psycopg2. csatlakozás](http://initd.org/psycopg/docs/connection.html) függvénnyel csatlakozik a Azure Database for PostgreSQL-rugalmas kiszolgálói adatbázishoz, és betölti az adatok egy SQL **Insert** utasítással. Az [cursor.exeCute](http://initd.org/psycopg/docs/cursor.html#execute) függvény végrehajtja az SQL-lekérdezést az adatbázison. 

```Python
import psycopg2
# Update connection string information 
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"
# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")
cursor = conn.cursor()
# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print("Finished dropping table (if existed)")
# Create a table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print("Finished creating table")
# Insert some data into the table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print("Inserted 3 rows of data")
# Clean up
conn.commit()
cursor.close()
conn.close()
```

A kód sikeres futtatásakor a következő kimenetet hozza létre:

![Parancssori kimenet](media/connect-python/2-example-python-output.png)

## <a name="read-data"></a>Adatok olvasása
A következő kódrészlet a Azure Database for PostgreSQL-rugalmas kiszolgálói adatbázishoz kapcsolódik, és az SQL SELECT utasítással az adatok olvasásához [cursor.exearanyos](http://initd.org/psycopg/docs/cursor.html#execute) **elemet** használja. Ez a függvény elfogad egy lekérdezést, és visszaad egy eredményhalmaz értékét a [cursor. fetchall ()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall)használatával. 

```Python
import psycopg2
# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"
# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")
cursor = conn.cursor()
# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()
# Print all rows
for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))
# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="update-data"></a>Adatok frissítése
A következő kódrészlet a Azure Database for PostgreSQL-rugalmas kiszolgálói adatbázishoz kapcsolódik, és az adatok frissítéséhez az SQL **Update** utasítással [cursor.exeCute](http://initd.org/psycopg/docs/cursor.html#execute) -t használja. 

```Python
import psycopg2
# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"
# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")
cursor = conn.cursor()
# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print("Updated 1 row of data")
# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="delete-data"></a>Adat törlése
A következő kódrészlet a Azure Database for PostgreSQL-rugalmas kiszolgálói adatbázishoz kapcsolódik, és az SQL **delete** utasítással [cursor.exearanyos](http://initd.org/psycopg/docs/cursor.html#execute) használatával törli a korábban beszúrt leltározási elemeket. 

```Python
import psycopg2
# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"
# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")
cursor = conn.cursor()
# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print("Deleted 1 row of data")
# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Adatbázis migrálása a dump és a Restore használatával](../howto-migrate-using-dump-and-restore.md)
