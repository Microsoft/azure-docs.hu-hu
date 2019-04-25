---
title: Csatlakozás az Azure Database for PostgreSQL-hez a Python használatával
description: Ez a rövid útmutató egy olyan Python-kódmintát biztosít, amellyel csatlakozhat egy PostgreSQL-hez készült Azure-adatbázishoz, és adatokat kérdezhet le arról.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc, devcenter
ms.devlang: python
ms.topic: quickstart
ms.date: 02/28/2018
ms.openlocfilehash: eb6b23d8f8c476ba41bea918456fdf7fb9782920
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60422150"
---
# <a name="azure-database-for-postgresql-use-python-to-connect-and-query-data"></a>Azure Database for postgresql-hez: Csatlakozás és adatlekérdezés a Python használatával
Ez a rövid útmutató ismerteti, hogyan használható a [Python](https://python.org) a PostgreSQL-hez készült Azure-adatbázishoz való csatlakozáshoz. Azt is bemutatjuk, hogy az SQL-utasítások használatával hogyan kérdezhetők le, illeszthetők be, frissíthetők és törölhetők az adatbázisban található adatok macOS, Ubuntu Linux és Windows platformon. A jelen cikkben ismertetett lépések feltételezik, hogy Ön rendelkezik fejlesztési tapasztalatokkal a Python használatával kapcsolatosan, a PostgreSQL-hez készült Azure Database használatában pedig még járatlan.

## <a name="prerequisites"></a>Előfeltételek
A rövid útmutató az alábbi útmutatók valamelyikében létrehozott erőforrásokat használja kiindulópontként:
- [DB létrehozása – portál](quickstart-create-server-database-portal.md)
- [DB létrehozása – CLI](quickstart-create-server-database-azure-cli.md)

Emellett a következőkre van szükség:
- telepített [python](https://www.python.org/downloads/)
- telepített [pip](https://pip.pypa.io/en/stable/installing/) csomag (a pip már telepítve van, ha a [python.org](https://python.org) webhelyről letöltött Python 2 >=2.7.9 vagy Python 3 >=3.4 bináris fájlokat használ.

## <a name="install-the-python-connection-libraries-for-postgresql"></a>A PostgreSQL-hez készült Python-adatkapcsolattárak telepítése
Telepítse a [psycopg2](http://initd.org/psycopg/docs/install.html) csomagot, amely lehetővé teszi a csatlakozást és az adatbázis lekérdezését. A psycopg2 a [PyPI webhelyen érhető el](https://pypi.python.org/pypi/psycopg2/) [kerék](https://pythonwheels.com/) csomagok formájában a legelterjedtebb platformokhoz (Linux, OSX, Windows). Használjon pip-telepítést az összes függőséget magában foglaló modul bináris verziójának beszerzéséhez.

1. A számítógépen indítsa el a parancssori felületet:
    - Linuxon indítsa el a Bash felületet.
    - MacOS rendszeren indítsa el a Terminal eszközt.
    - Windows rendszeren indítsa el a parancssort a Start menüből.
2. Ellenőrizze, hogy a pip legfrissebb verzióját használja-e, az alábbihoz hasonló parancs futtatásával:
    ```cmd
    pip install -U pip
    ```

3. Futtassa az alábbi parancsot a psycopg2 csomag telepítéséhez:
    ```cmd
    pip install psycopg2
    ```

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése
Kérje le a PostgreSQL-hez készült Azure-adatbázishoz való csatlakozáshoz szükséges kapcsolatadatokat. Szüksége lesz a teljes kiszolgálónévre és a bejelentkezési hitelesítő adatokra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Az Azure Portal bal oldali menüjében kattintson a **Minden erőforrás** lehetőségre, és keressen rá a létrehozott kiszolgálóra (például **mydemoserver**).
3. Kattintson a kiszolgálónévre.
4. A kiszolgáló **Áttekintés** paneléről jegyezze fel a **Kiszolgálónevet** és a **Kiszolgáló-rendszergazdai bejelentkezési nevet**. Ha elfelejti a jelszavát, ezen a panelen új jelszót is tud kérni.
 ![Azure Database for PostgreSQL-kiszolgáló neve](./media/connect-python/1-connection-string.png)

## <a name="how-to-run-python-code"></a>Python-kód futtatása
Ez a cikk összesen négy kódmintát tartalmaz, amelyek mindegyike egy adott funkciót hajt végre. Az alábbi utasítások alapján szövegfájlt hozhat létre, kódblokkot szúrhat be, majd mentheti a fájlt későbbi futtatáshoz. Mindenképpen négy külön fájlt hozzon létre, mindegyik blokk számára egyet.

- Hozzon létre egy új fájlt a kedvenc szövegszerkesztőjével.
- Másolja és illessze be az alábbi szakaszokban található kódminták egyikét a szövegfájlba. Cserélje le a **gazdagép**, az **adatbázisnév**, a **felhasználó** és a **jelszó** paramétereit azokra az értékekre, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.
- Mentse a fájlt a .py kiterjesztéssel (például postgres.py) a projektmappába. Ha Windows rendszert futtat, az UTF-8 kódolást válassza a fájl mentésekor. 
- Indítsa el a parancssort, a Terminált vagy a Bash felületet, és lépjen a projektmappába, például: `cd postgres`.
-  A kód futtatásához írja be a Python-parancsot, majd a fájlnevet, például: `Python postgres.py`.

> [!NOTE]
> A Python 3-as verziójú-től kezdődően a hiba jelenhet `SyntaxError: Missing parentheses in call to 'print'` az alábbi kódblokkok futtatásakor: Ha ez történik, cserélje le a `print "string"` parancs minden hívását egy zárójelet használó függvényhívással, például a következővel: `print("string")`.

## <a name="connect-create-table-and-insert-data"></a>Csatlakozás, táblák létrehozása és adatok beszúrása
Az alábbi kód használatával csatlakozhat és töltheti be az adatokat az **INSERT** SQL-utasítással használt [psycopg2.connect](http://initd.org/psycopg/docs/connection.html) függvény segítségével. A [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) függvény az SQL-lekérdezés PostgreSQL-adatbázison való végrehajtására szolgál. Cserélje le a gazdagép, az adatbázisnév, a felhasználó és a jelszó paramétereit azokra az értékekre, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mydemoserver.postgres.database.azure.com"
user = "mylogin@mydemoserver"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print "Finished dropping table (if existed)"

# Create table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print "Finished creating table"

# Insert some data into table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print "Inserted 3 rows of data"

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

A kód sikeres futtatása után a kimenet a következőképpen néz ki:

![Parancssori kimenet](media/connect-python/2-example-python-output.png)

## <a name="read-data"></a>Adatok olvasása
Az alábbi kód használatával végezheti el a beillesztett adatok olvasását a **SELECT** SQL-utasítással használt [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) függvény segítségével. Ez a függvény lekérdezést fogad el, és olyan eredményt ad vissza, amely a [cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall) használatával ismételhető. Cserélje le a gazdagép, az adatbázisnév, a felhasználó és a jelszó paramétereit azokra az értékekre, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mydemoserver.postgres.database.azure.com"
user = "mylogin@mydemoserver"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print "Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2]))

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="update-data"></a>Adatok frissítése
Az alábbi kód használatával végezheti el a korábban beillesztett leltári sor frissítését az **UPDATE** SQL-utasítással használt [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) függvény segítségével. Cserélje le a gazdagép, az adatbázisnév, a felhasználó és a jelszó paramétereit azokra az értékekre, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mydemoserver.postgres.database.azure.com"
user = "mylogin@mydemoserver"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print "Updated 1 row of data"

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="delete-data"></a>Adat törlése
Az alábbi kód használatával végezheti el valamely korábban beillesztett leltári tétel törlését a **DELETE** SQL-utasítással használt [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) függvény segítségével. Cserélje le a gazdagép, az adatbázisnév, a felhasználó és a jelszó paramétereit azokra az értékekre, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mydemoserver.postgres.database.azure.com"
user = "mylogin@mydemoserver"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print "Deleted 1 row of data"

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálással és importálással](./howto-migrate-using-export-and-import.md)
