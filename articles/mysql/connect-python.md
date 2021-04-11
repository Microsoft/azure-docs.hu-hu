---
title: 'Gyors útmutató: kapcsolat a Python használatával – Azure Database for MySQL'
description: Ez a rövid útmutató számos Python-mintakódot biztosít, amelyekkel csatlakozhat a MySQL-hez készült Azure-adatbázishoz, illetve adatokat kérdezhet le róla.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom:
- mvc
- seo-python-october2019
- devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: a4391ecb7175b0e473b47cc3de43fd113795bc6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889025"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-mysql"></a>Rövid útmutató: a Python használatával csatlakozhat és lekérdezheti Azure Database for MySQL

Ebben a rövid útmutatóban a Python használatával csatlakozik egy Azure Database for MySQLhoz. Ezután SQL-utasításokkal adatokat lehet lekérdezni, beszúrni, frissíteni és törölni az adatbázisban a Mac, Ubuntu Linux és Windows platformokról. 

## <a name="prerequisites"></a>Előfeltételek
Ehhez a rövid útmutatóhoz a következőkre lesz szüksége:

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free).
- Azure Database for MySQL önálló kiszolgáló létrehozása [Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md) használatával <br/> vagy az [Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md) -vel, ha még nem rendelkezik ilyennel.
- Attól függően, hogy nyilvános vagy privát hozzáférést használ-e, a kapcsolat engedélyezéséhez hajtsa végre az alábbi műveletek **egyikét** .

   |Művelet| Kapcsolati mód|Útmutató|
   |:--------- |:--------- |:--------- |
   | **Tűzfalszabályok konfigurálása** | Nyilvános | [Portál](./howto-manage-firewall-using-portal.md) <br/> [Parancssori felület](./howto-manage-firewall-using-cli.md)|
   | **Szolgáltatási végpont konfigurálása** | Nyilvános | [Portál](./howto-manage-vnet-using-portal.md) <br/> [Parancssori felület](./howto-manage-vnet-using-cli.md)| 
   | **Privát hivatkozás konfigurálása** | Személyes | [Portál](./howto-configure-privatelink-portal.md) <br/> [Parancssori felület](./howto-configure-privatelink-cli.md) | 

- [Adatbázis és nem rendszergazda felhasználó létrehozása](./howto-create-users.md)

## <a name="install-python-and-the-mysql-connector"></a>A Python és a MySQL-összekötő telepítése

Telepítse a Pythont és a Pythonhoz készült MySQL-összekötőt a számítógépre a következő lépések végrehajtásával: 

> [!NOTE]
> Ez a rövid [útmutató a MySQL Connector/Python fejlesztői útmutatót](https://dev.mysql.com/doc/connector-python/en/)használja.

1. Töltse le és telepítse a [Python 3,7-es vagy újabb](https://www.python.org/downloads/) verzióját az operációs rendszerének. Ügyeljen arra, hogy a Pythont hozzáadja a szolgáltatáshoz `PATH` , mert a MySQL-összekötő ehhez szükséges.
   
2. Nyisson meg egy parancssort vagy egy `bash` rendszerhéjat, és `python -V` a nagybetűs V kapcsolóval futtassa a Python-verziót.
   
3. A `pip` csomag telepítőjének részét képezi a Python legújabb verziói. Frissítsen `pip` a legújabb verzióra a futtatásával `pip install -U pip` . 
   
   Ha `pip` nincs telepítve, letöltheti és telepítheti azt a használatával `get-pip.py` . További információ: [telepítés](https://pip.pypa.io/en/stable/installing/). 
   
4. `pip`A használatával telepítheti a MySQL-összekötőt a Pythonhoz és annak függőségeihez:
   
   ```bash
   pip install mysql-connector-python
   ```
   
[Problémák léptek fel? Tudassa velünk](https://aka.ms/mysql-doc-feedback)

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése

Szerezze be a Azure Database for MySQLhoz való kapcsolódáshoz szükséges kapcsolati adatokat a Azure Portalból. Szüksége lesz a kiszolgáló nevére, az adatbázis nevére és a bejelentkezési hitelesítő adatokra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
   
1. A portálon keresse meg és válassza ki a létrehozott Azure Database for MySQL-kiszolgálót, például **mydemoserver**.
   
   :::image type="content" source="./media/connect-python/1_server-overview-name-login.png" alt-text="A MySQL-hez készült Azure Database-kiszolgáló neve":::
   
1. A kiszolgáló **Áttekintés** lapján jegyezze fel a **kiszolgáló nevét** és a **kiszolgáló-rendszergazdai bejelentkezési nevet**. Ha elfelejti a jelszavát, akkor ezen a lapon is visszaállíthatja a jelszót.
   
   :::image type="content" source="./media/connect-python/azure-database-for-mysql-server-overview-name-login.png" alt-text="Azure Database for MySQL kiszolgáló neve 2":::

## <a name="step-1-create-a-table-and-insert-data"></a>1. lépés: tábla létrehozása és az adatbeszúrás

A következő kód használatával csatlakozhat a kiszolgálóhoz és az adatbázishoz, létrehozhat egy táblát, és betöltheti az adatok betöltését egy **Insert** SQL-utasítás használatával. A kód importálja a MySQL. Connector függvénytárat, és a metódust használja:
- a [csatlakozási ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) függvényt a konfigurációs gyűjtemény [argumentumai](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) használatával Azure Database for MySQLhoz való kapcsolódáshoz. 
- [cursor.exeCute ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) metódus hajtja végre az SQL-lekérdezést a MySQL-adatbázison. 
- [kurzor. Bezárás ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-close.html) , ha egy kurzor használatával végzett.
- [Conn. Bezárás ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlconnection-close.html) a kapcsolódás bezárásához.

> [!IMPORTANT]
> - Az SSL alapértelmezés szerint engedélyezve van. Előfordulhat, hogy le kell töltenie a [DIGICERTGLOBALROOTG2 SSL-tanúsítványt](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) a helyi környezetből való kapcsolódáshoz.
> - Cserélje le a `<mydemoserver>` , `<myadmin>` , `<mypassword>` , és `<mydatabase>` helyőrzőket a MySQL-kiszolgáló és-adatbázis értékeire.

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>',
  'client_flags': [mysql.connector.ClientFlag.SSL],
  'ssl_ca': '/var/wwww/html/DigiCertGlobalRootG2.crt.pem'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Drop previous table of same name if one exists
  cursor.execute("DROP TABLE IF EXISTS inventory;")
  print("Finished dropping table (if existed).")

  # Create table
  cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
  print("Finished creating table.")

  # Insert some data into table
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
  print("Inserted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

[Problémák léptek fel? Tudassa velünk](https://aka.ms/mysql-doc-feedback)

## <a name="step-2-read-data"></a>2. lépés: az adatolvasás

A következő kóddal csatlakozhat, és beolvashatja az adatokat a **SELECT** SQL-utasítással. A kód importálja a MySQL. Connector függvénytárat, és a [cursor.exeCute ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) metódust használja, és végrehajtja az SQL-lekérdezést a MySQL-adatbázison. 

A kód beolvassa az adatsorokat a [fetchall ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html) metódussal, megtartja az eredményhalmaz egy gyűjtemény sorában, és egy `for` iterációt használ a sorok áthurkolása érdekében.

```python
  # Read data
  cursor.execute("SELECT * FROM inventory;")
  rows = cursor.fetchall()
  print("Read",cursor.rowcount,"row(s) of data.")

  # Print all rows
  for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

```

## <a name="step-3-update-data"></a>3. lépés: az Adatfrissítés

Az alábbi kód használatával csatlakozhat és végezheti el az adatok módosítását egy **UPDATE** SQL-utasítás segítségével. A kód importálja a MySQL. Connector függvénytárat, és a [cursor.exeCute ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) metódust használja, és végrehajtja az SQL-lekérdezést a MySQL-adatbázison. 

```python
  # Update a data row in the table
  cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
  print("Updated",cursor.rowcount,"row(s) of data.")
```

## <a name="step-4-delete-data"></a>4. lépés: az adattörlés

A következő kód használatával csatlakozhat, és eltávolíthatja az adatokat a **DELETE** SQL-utasítással. A kód importálja a MySQL. Connector függvénytárat, és a [cursor.exeCute ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) metódust használja, és végrehajtja az SQL-lekérdezést a MySQL-adatbázison. 

```python

  # Delete a data row in the table
  cursor.execute("DELETE FROM inventory WHERE name=%(param1)s;", {'param1':"orange"})
  print("Deleted",cursor.rowcount,"row(s) of data.")
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
> [Azure Database for MySQL-kiszolgáló kezelése a portál használatával](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Azure Database for MySQL-kiszolgáló kezelése a parancssori felület használatával](./how-to-manage-single-server-cli.md)

[Nem találja, amit keres? Tudassa velünk.](https://aka.ms/mysql-doc-feedback)
