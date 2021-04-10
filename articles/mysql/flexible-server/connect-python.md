---
title: 'Gyors útmutató: kapcsolat a Python-Azure Database for MySQL-rugalmas kiszolgáló használatával'
description: Ez a rövid útmutató számos Python-kódrészletet tartalmaz, amelyekkel csatlakozhat, és lekérdezheti Azure Database for MySQL-rugalmas kiszolgáló adatait.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: e99beb024690bf888d0acbb747e98252eced7e06
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110134"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server"></a>Rövid útmutató: a Python használata a Azure Database for MySQL rugalmas kiszolgálóval való kapcsolódáshoz és az adatlekérdezéshez

> [!IMPORTANT] 
> Azure Database for MySQL – a rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Ebben a rövid útmutatóban egy Azure Database for MySQL rugalmas kiszolgálóhoz csatlakozik a Python használatával. Ezután SQL-utasításokkal adatokat lehet lekérdezni, beszúrni, frissíteni és törölni az adatbázisban a Mac, Ubuntu Linux és Windows platformokról. 

Ez a cikk azt feltételezi, hogy már ismeri a fejlesztést a Python használatával, de most ismerkedik a Azure Database for MySQL rugalmas kiszolgálóval.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Egy Azure Database for MySQL rugalmas kiszolgáló. Rugalmas kiszolgáló létrehozásához olvassa el a [Azure Database for MySQL rugalmas kiszolgáló létrehozása Azure Portal használatával](./quickstart-create-server-portal.md) vagy [Az Azure CLI-vel történő Azure Database for MySQL rugalmas kiszolgáló](./quickstart-create-server-cli.md)létrehozása című témakört.

## <a name="preparing-your-client-workstation"></a>Az ügyfél munkaállomásának előkészítése
- Ha a rugalmas kiszolgálót *privát hozzáféréssel (VNet-integrációval)* hozta létre, akkor a kiszolgálóval egy olyan erőforráshoz kell csatlakoznia, amely a kiszolgálóval megegyező VNet belül található. Létrehozhat egy virtuális gépet, és hozzáadhatja azt a rugalmas kiszolgálóval létrehozott VNet. Lásd: [Azure Database for MySQL rugalmas kiszolgálói virtuális hálózat létrehozása és kezelése az Azure CLI-](./how-to-manage-virtual-network-cli.md)vel.
- Ha a rugalmas kiszolgálót *nyilvános hozzáféréssel (engedélyezett IP-címekkel)* hozta létre, akkor a helyi IP-címét felveheti a kiszolgálón található tűzfalszabályok listájára. Tekintse át [Azure Database for MySQL rugalmas kiszolgálói tűzfalszabályok létrehozása és kezelése az Azure CLI használatával](./how-to-manage-firewall-cli.md)című témakört.

## <a name="install-python-and-the-mysql-connector"></a>A Python és a MySQL-összekötő telepítése

Telepítse a Pythont és a Pythonhoz készült MySQL-összekötőt a számítógépre a következő lépések végrehajtásával: 

> [!NOTE]
> Ez a rövid útmutató egy nyers SQL-lekérdezési módszert használ a MySQL-hez való kapcsolódáshoz. Ha webes keretrendszert használ, használja az ajánlott összekötőt a keretrendszerhez, például [mysqlclient](https://pypi.org/project/mysqlclient/) for Django.

1. Töltse le és telepítse a [Python 3,7-es vagy újabb](https://www.python.org/downloads/) verzióját az operációs rendszerének. Ügyeljen arra, hogy a Pythont hozzáadja a szolgáltatáshoz `PATH` , mert a MySQL-összekötő ehhez szükséges.
   
1. Nyisson meg egy parancssort vagy egy `bash` rendszerhéjat, és `python -V` a nagybetűs V kapcsolóval futtassa a Python-verziót.
   
1. A `pip` csomag telepítőjének részét képezi a Python legújabb verziói. Frissítsen `pip` a legújabb verzióra a futtatásával `pip install -U pip` . 
   
   Ha `pip` nincs telepítve, letöltheti és telepítheti azt a használatával `get-pip.py` . További információ: [telepítés](https://pip.pypa.io/en/stable/installing/). 
   
1. `pip`A használatával telepítheti a MySQL-összekötőt a Pythonhoz és annak függőségeihez:
   
   ```bash
   pip install mysql-connector-python
   ```
   
   A [MySQL.com](https://dev.mysql.com/downloads/connector/python/)-ből is telepítheti a MySQL-hez készült Python-összekötőt. További információ a Pythonhoz készült MySQL-összekötőről: [MySQL Connector/Python fejlesztői útmutató](https://dev.mysql.com/doc/connector-python/en/). 

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése

Szerezze be a Azure Database for MySQL rugalmas kiszolgálóhoz való kapcsolódáshoz szükséges kapcsolati adatokat a Azure Portalról. Szüksége lesz a kiszolgáló nevére, az adatbázis nevére és a bejelentkezési hitelesítő adatokra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
   
1. A portálon keresse meg és válassza ki a létrehozott Azure Database for MySQL rugalmas kiszolgálót, például **mydemoserver**.
   
   <!---:::image type="content" source="./media/connect-python/1_server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::-->
   
1. A kiszolgáló **Áttekintés** lapján jegyezze fel a **kiszolgáló nevét** és a **kiszolgáló-rendszergazdai bejelentkezési nevet**. Ha elfelejti a jelszavát, akkor ezen a lapon is visszaállíthatja a jelszót.
   
   <!---:::image type="content" source="./media/connect-python/azure-database-for-mysql-server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::-->

## <a name="code-samples"></a>Kódminták

### <a name="run-below-mentioned-python-code-samples"></a>Az alább említett Python-kód mintáinak futtatása
A cikkben szereplő kódok mindegyike esetében:

1. Hozzon létre egy új fájlt egy szövegszerkesztőben.
1. Adja hozzá a példában szereplő kódot a fájlhoz. A kódban cserélje le a `<mydemoserver>` , `<myadmin>` ,, `<mypassword>` és `<mydatabase>` helyőrzőket a MySQL-kiszolgáló és-adatbázis értékeire.
1. Mentse a fájlt a Project mappába egy *.* file kiterjesztésű bővítménnyel, például *C:\pythonmysql\createtable.py* vagy */Home/username/pythonmysql/createtable.py elérési úton*.
1. A kód futtatásához nyisson meg egy parancssort vagy egy `bash` rendszerhéjat, és módosítsa a könyvtárat a Project mappájába, például: `cd pythonmysql` . Írja be a `python` parancsot, majd a fájlnevet, `python createtable.py` majd nyomja le az ENTER billentyűt. 
   
   > [!NOTE]
   > Windows rendszeren, ha *python.exe* nem található, lehet, hogy hozzá kell adnia a Python elérési útját a PATH környezeti változóhoz, vagy meg kell adnia a *python.exe* teljes elérési útját, például: `C:\python27\python.exe createtable.py` .

### <a name="create-a-table-and-insert-data"></a>Tábla létrehozása és az adatbeszúrás

A következő kód használatával csatlakozhat a kiszolgálóhoz és az adatbázishoz, létrehozhat egy táblát, és betöltheti az adatok betöltését egy **Insert** SQL-utasítás használatával. 

A kód importálja a MySQL. Connector függvénytárat, és a [csatlakozás ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) függvény használatával csatlakozik a rugalmas kiszolgálóhoz a konfigurációs gyűjtemény [argumentumai](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) segítségével. A kód egy kurzort használ a kapcsolatban, és a [cursor.exeCute ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) metódus hajtja végre az SQL-lekérdezést a MySQL-adatbázison. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
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

### <a name="read-data"></a>Adatok olvasása

A következő kóddal csatlakozhat, és beolvashatja az adatokat a **SELECT** SQL-utasítással. 

A kód importálja a MySQL. Connector függvénytárat, és a [csatlakozás ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) függvény használatával csatlakozik a rugalmas kiszolgálóhoz a konfigurációs gyűjtemény [argumentumai](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) segítségével. A kód egy kurzort használ a kapcsolatban, és a [cursor.exeCute ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) metódus hajtja végre az SQL-lekérdezést a MySQL-adatbázison. 

A kód beolvassa az adatsorokat a [fetchall ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html) metódussal, megtartja az eredményhalmaz egy gyűjtemény sorában, és egy `for` iterációt használ a sorok áthurkolása érdekében.

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
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

  # Read data
  cursor.execute("SELECT * FROM inventory;")
  rows = cursor.fetchall()
  print("Read",cursor.rowcount,"row(s) of data.")

  # Print all rows
  for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

### <a name="update-data"></a>Adatok frissítése

Az alábbi kód használatával csatlakozhat és végezheti el az adatok módosítását egy **UPDATE** SQL-utasítás segítségével. 

A kód importálja a MySQL. Connector függvénytárat, és a [csatlakozás ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) függvény használatával csatlakozik a rugalmas kiszolgálóhoz a konfigurációs gyűjtemény [argumentumai](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) segítségével. A kód egy kurzort használ a kapcsolatban, és a [cursor.exeCute ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) metódus hajtja végre az SQL-lekérdezést a MySQL-adatbázison. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
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

  # Update a data row in the table
  cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
  print("Updated",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

### <a name="delete-data"></a>Adat törlése

A következő kód használatával csatlakozhat, és eltávolíthatja az adatokat a **DELETE** SQL-utasítással. 

A kód importálja a MySQL. Connector függvénytárat, és a [csatlakozás ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) függvény használatával csatlakozik a rugalmas kiszolgálóhoz a konfigurációs gyűjtemény [argumentumai](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) segítségével. A kód egy kurzort használ a kapcsolatban, és a [cursor.exeCute ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) metódus hajtja végre az SQL-lekérdezést a MySQL-adatbázison. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established.")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password.")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist.")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Delete a data row in the table
  cursor.execute("DELETE FROM inventory WHERE name=%(param1)s;", {'param1':"orange"})
  print("Deleted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="next-steps"></a>Következő lépések
- [Titkosított kapcsolat Transport Layer Security (TLS 1,2) használatával Azure Database for MySQL-rugalmas kiszolgálón](./how-to-connect-tls-ssl.md).
- További információ a [Azure Database for MySQL rugalmas kiszolgáló hálózatkezeléséről](./concepts-networking.md).
- [Azure Database for MySQL rugalmas kiszolgálói tűzfalszabályok létrehozása és kezelése a Azure Portal használatával](./how-to-manage-firewall-portal.md).
- [Azure Database for MySQL rugalmas kiszolgálói virtuális hálózat létrehozása és kezelése Azure Portal használatával](./how-to-manage-virtual-network-portal.md).
