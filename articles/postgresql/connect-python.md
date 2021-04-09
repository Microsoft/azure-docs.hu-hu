---
title: 'Gyors útmutató: kapcsolat a Python-Azure Database for PostgreSQL-Single Serverrel'
description: Ez a rövid útmutató olyan Python-kódrészleteket tartalmaz, amelyekkel összekapcsolhatók és lekérhető Azure Database for PostgreSQL – egyetlen kiszolgálóról származó adatok lekérdezése.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devcenter, devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: 6c589f3f0913609640ac14534c170c741c16a084
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048170"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Rövid útmutató: a Python használata az Azure Database for PostgreSQL-kiszolgálóval való kapcsolódáshoz és az adatlekérdezéshez

Ebből a rövid útmutatóból megtudhatja, hogyan csatlakozhat az adatbázishoz Azure Database for PostgreSQL egyetlen kiszolgálón, és hogyan futtathat SQL-utasításokat a Python macOS, Ubuntu Linux vagy Windows rendszeren történő lekérdezéséhez.

> [!TIP]
> Ha Django-alkalmazást szeretne felépíteni a PostgreSQL-sel, akkor adja ki az oktatóanyagot, [és telepítsen egy Django-webalkalmazást a PostgreSQL](../app-service/tutorial-python-postgresql-app.md) oktatóanyaggal.


## <a name="prerequisites"></a>Előfeltételek
Ehhez a rövid útmutatóhoz a következőkre lesz szüksége:

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free).
- Azure Database for PostgreSQL önálló kiszolgáló létrehozása [Azure Portal](./quickstart-create-server-database-portal.md) használatával <br/> vagy az [Azure CLI](./quickstart-create-server-database-azure-cli.md) -vel, ha még nem rendelkezik ilyennel.
- Attól függően, hogy nyilvános vagy privát hozzáférést használ-e, a kapcsolat engedélyezéséhez hajtsa végre az alábbi műveletek **egyikét** .

  |Művelet| Kapcsolati mód|Útmutató|
  |:--------- |:--------- |:--------- |
  | **Tűzfalszabályok konfigurálása** | Nyilvános | [Portál](./howto-manage-firewall-using-portal.md) <br/> [Parancssori felület](./howto-manage-firewall-using-cli.md)|
  | **Szolgáltatási végpont konfigurálása** | Nyilvános | [Portál](./howto-manage-vnet-using-portal.md) <br/> [Parancssori felület](./howto-manage-vnet-using-cli.md)|
  | **Privát hivatkozás konfigurálása** | Személyes | [Portál](./howto-configure-privatelink-portal.md) <br/> [Parancssori felület](./howto-configure-privatelink-cli.md) |

- [Python](https://www.python.org/downloads/) 2,7 vagy 3.6 +.

- Legújabb [pip](https://pip.pypa.io/en/stable/installing/) -csomag telepítője.
- Telepítse [](https://pypi.python.org/pypi/psycopg2/) a psycopg2 `pip install psycopg2` -t egy terminál vagy parancssori ablak használatával. További információ: [How to install `psycopg2` ](http://initd.org/psycopg/docs/install.html).

## <a name="get-database-connection-information"></a>Adatbázis-kapcsolatok adatainak beolvasása
Azure Database for PostgreSQL-adatbázishoz való csatlakozáshoz a teljes kiszolgálónév és a bejelentkezési hitelesítő adatok szükségesek. Ezt az információt a Azure Portalból kérheti le.

1. A [Azure Portal](https://portal.azure.com/)keresse meg és válassza ki a Azure Database for PostgreSQL-kiszolgáló nevét.
1. A kiszolgáló **Áttekintés** lapján másolja a teljes **kiszolgálónevet** és a **rendszergazdai felhasználónevet**. A teljes **kiszolgálónév** mindig a form *\<my-server-name> . postgres.database.Azure.com*, a **rendszergazdai Felhasználónév** pedig mindig *\<my-admin-username>@\<my-server-name>* az űrlap.

   Szüksége lesz a rendszergazdai jelszavára is. Ha elfelejti, ezt a lapról állíthatja vissza.

   :::image type="content" source="./media/connect-python/1-connection-string.png" alt-text="Azure Database for PostgreSQL-kiszolgáló neve":::

> [!IMPORTANT]
>  Cserélje le a következő értékeket:
>   - `<server-name>` és a `<admin-username>` Azure Portalból másolt értékekkel.
>   - `<admin-password>` a kiszolgáló jelszavával.
>   - `<database-name>` a kiszolgáló létrehozásakor a rendszer automatikusan létrehozta a *postgres* nevű alapértelmezett adatbázist. Átnevezheti az adatbázist, vagy [létrehozhat egy új adatbázist](https://www.postgresql.org/docs/9.0/sql-createdatabase.html) az SQL-parancsok használatával.

## <a name="step-1-connect-and-insert-data"></a>1. lépés: az adatkapcsolat és az adatbeszúrás
Az alábbi kódrészlet a következő példában csatlakozik a Azure Database for PostgreSQL-adatbázishoz a használatával
-  [psycopg2. Csatlakoztassa](http://initd.org/psycopg/docs/connection.html) a függvényt, és betölti az adatok egy SQL **Insert** utasítással.
- [cursor.exeCute](http://initd.org/psycopg/docs/cursor.html#execute) függvény végrehajtja az SQL-lekérdezést az adatbázison.

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

:::image type="content" source="media/connect-python/2-example-python-output.png" alt-text="Parancssori kimenet":::


[Problémák léptek fel? Tudassa velünk](https://aka.ms/postgres-doc-feedback)

## <a name="step-2-read-data"></a>2. lépés: az adatolvasás
A következő mintakód a Azure Database for PostgreSQL-adatbázishoz kapcsolódik, és használja
- [cursor.exe](http://initd.org/psycopg/docs/cursor.html#execute) az SQL **Select** utasítással az adatok olvasásához.
- a [cursor. fetchall ()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall) fogad egy lekérdezést, és visszaadja egy eredményhalmaz értékét a következő használatával:

```Python

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))


```
[Problémák léptek fel? Tudassa velünk](https://aka.ms/postgres-doc-feedback)

## <a name="step-3-update-data"></a>3. lépés: az Adatfrissítés
Az alábbi kódrészlet az SQL **Update** utasítással frissíti az adatok frissítéséhez [cursor.exeCute](http://initd.org/psycopg/docs/cursor.html#execute) kifejezést.

```Python

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print("Updated 1 row of data")

```
[Problémák léptek fel? Tudassa velünk](https://aka.ms/postgres-doc-feedback)

## <a name="step-5-delete-data"></a>5. lépés: az adattörlés
A következő kódrészlet a korábban beszúrt leltári elemek törléséhez az SQL **delete** utasítással [cursor.exearanyos](http://initd.org/psycopg/docs/cursor.html#execute) .

```Python

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print("Deleted 1 row of data")

```

[Problémák léptek fel? Tudassa velünk](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a rövid útmutatóban használt összes erőforrás törléséhez törölje az erőforráscsoportot a következő parancs használatával:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Azure Database for MySQL-kiszolgáló kezelése a portál használatával](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Azure Database for MySQL-kiszolgáló kezelése a parancssori felület használatával](./how-to-manage-server-cli.md)<br/>

[Nem találja, amit keres? Tudassa velünk.](https://aka.ms/postgres-doc-feedback)
