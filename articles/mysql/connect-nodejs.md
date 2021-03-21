---
title: 'Gyors útmutató: kapcsolat Node.js használatával – Azure Database for MySQL'
description: Ez a rövid útmutató számos Node.js-mintakódot biztosít, amelyekkel csatlakozhat a MySQL-hez készült Azure-adatbázishoz, illetve adatokat kérdezhet le róla.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-js
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 12/11/2020
ms.openlocfilehash: 6a9134e13e3145daea1eed81c4aa8795a0a49950
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97588233"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-in-azure-database-for-mysql"></a>Gyors útmutató: Node.js használata az adatAzure Database for MySQLhoz való kapcsolódáshoz és adatlekérdezéshez

Ebben a rövid útmutatóban Node.js használatával csatlakozik egy Azure Database for MySQLhoz. Ezután SQL-utasításokkal adatokat lehet lekérdezni, beszúrni, frissíteni és törölni az adatbázisban a Mac, Ubuntu Linux és Windows platformokról. 

Ez a témakör azt feltételezi, hogy már ismeri a fejlesztést a Node.js használatával, de most ismerkedik a Azure Database for MySQLval.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Egy Azure Database for MySQL-kiszolgáló. [Hozzon létre egy Azure Database for MySQL kiszolgálót Azure Portal használatával](quickstart-create-mysql-server-database-using-azure-portal.md) , vagy [hozzon létre egy Azure Database for MySQL kiszolgálót az Azure CLI használatával](quickstart-create-mysql-server-database-using-azure-cli.md).

> [!IMPORTANT] 
> Győződjön meg arról, hogy az IP-cím, amelyhez csatlakozik, a [Azure Portal](./howto-manage-firewall-using-portal.md) vagy az [Azure CLI](./howto-manage-firewall-using-cli.md) használatával adja hozzá a kiszolgáló tűzfalszabály-szabályait.

## <a name="install-nodejs-and-the-mysql-connector"></a>A Node.js és a MySQL-összekötő telepítése

A platformtól függően kövesse a megfelelő szakaszban található utasításokat a [Node.js](https://nodejs.org)telepítéséhez. A NPM használatával telepítse a [MySQL](https://www.npmjs.com/package/mysql) -csomagot és annak függőségeit a projekt mappájába.

### <a name="windows"></a>Windows

1. Látogasson el a [Node.js letöltési oldalára](https://nodejs.org/en/download/), majd válassza ki a kívánt Windows-telepítési lehetőséget.
2. Hozzon létre egy helyi projektmappát, például: `nodejsmysql`. 
3. Nyissa meg a parancssort, majd módosítsa a könyvtárat a Project mappába, például `cd c:\nodejsmysql\`
4. A NPM eszköz futtatásával telepítse a MySQL-függvénytárat a Project mappába.

   ```cmd
   cd c:\nodejsmysql\
   "C:\Program Files\nodejs\npm" install mysql
   "C:\Program Files\nodejs\npm" list
   ```

5. Ellenőrizze a telepítést a `npm list` kimeneti szöveg ellenőrzésével. Új javítások kiadásakor a verziószám változhat.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. A **Node.js** és az **npm**, a Node.js csomagkezelőjének telepítéséhez futtassa a következő parancsokat.

   ```bash
    # Using Ubuntu
    curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -
    sudo apt-get install -y nodejs

    # Using Debian, as root
    curl -sL https://deb.nodesource.com/setup_14.x | bash -
    apt-get install -y nodejs
   ```

2. Futtassa a következő parancsokat egy projekt mappájának létrehozásához, `mysqlnodejs` és telepítse a MySQL-csomagot a mappába.

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql
   npm list
   ```
3. Ellenőrizze a telepítést a NPM-lista kimeneti szövegének ellenőrzésével. Új javítások kiadásakor a verziószám változhat.

### <a name="macos"></a>macOS

1. Látogasson el a [Node.js letöltések oldalra](https://nodejs.org/en/download/), majd válassza ki a MacOS-telepítőt.

2. Futtassa a következő parancsokat egy projekt mappájának létrehozásához, `mysqlnodejs` és telepítse a MySQL-csomagot a mappába.

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql
   npm list
   ```

3. Ellenőrizze a telepítést a `npm list` kimeneti szöveg ellenőrzésével. Új javítások kiadásakor a verziószám változhat.

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése

Kérje le a MySQL-hez készült Azure Database-hez való csatlakozáshoz szükséges kapcsolatadatokat. Szüksége lesz a teljes kiszolgálónévre és a bejelentkezési hitelesítő adatokra.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/).
2. Azure Portal bal oldali menüjében válassza a **minden erőforrás** elemet, majd keresse meg a létrehozott kiszolgálót (például **mydemoserver**).
3. Válassza ki a kiszolgálónevet.
4. A kiszolgáló **Áttekintés** paneléről jegyezze fel a **Kiszolgálónevet** és a **Kiszolgáló-rendszergazdai bejelentkezési nevet**. Ha elfelejti a jelszavát, ezen a panelen új jelszót is tud kérni.
 :::image type="content" source="./media/connect-nodejs/server-name-azure-database-mysql.png" alt-text="A MySQL-hez készült Azure Database-kiszolgáló neve":::

## <a name="running-the-javascript-code-in-nodejs"></a>A JavaScript-kód futtatása a Node.js-ben

1. Illessze be a JavaScript-kódot szövegfájlokba, és mentse őket egy projektmappába .js kiterjesztéssel (például: C:\nodejsmysql\createtable.js vagy /home/username/nodejsmysql/createtable.js).
2. Nyissa meg a parancssort vagy a bash rendszerhéjt, majd módosítsa a könyvtárat a projekt mappájába `cd nodejsmysql` .
3. Az alkalmazás futtatásához írja be a Node parancsot, majd a fájlnevet, például: `node createtable.js` .
4. Ha Windows rendszeren nem található a Node.js-alkalmazás a path környezeti változóban, előfordulhat, hogy a teljes elérési utat kell használnia a Node.js-alkalmazás elindításához, például: `"C:\Program Files\nodejs\node.exe" createtable.js`

## <a name="connect-create-table-and-insert-data"></a>Csatlakozás, táblák létrehozása és adatok beszúrása

A következő kód használatával csatlakozhat, és betöltheti az adatokat a **CREATE TABLE** használatával, és  **beillesztheti** az SQL-utasításokba.

A [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) metódus a MySQL-kiszolgálóhoz való csatlakozásra szolgál. A [connect()](https://github.com/mysqljs/mysql#establishing-connections) függvény a kiszolgálóval való kapcsolat létesítésére szolgál. A [query()](https://github.com/mysqljs/mysql#performing-queries) függvény az SQL-lekérdezés MySQL-adatbázison való végrehajtására szolgál. 

Cserélje le a `host`, `user`, `password` és `database` paramétert azokkal az értékekkel, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
    if (err) { 
        console.log("!!! Cannot connect !!! Error:");
        throw err;
    }
    else
    {
       console.log("Connection established.");
           queryDatabase();
    }
});

function queryDatabase(){
    conn.query('DROP TABLE IF EXISTS inventory;', function (err, results, fields) { 
        if (err) throw err; 
        console.log('Dropped inventory table if existed.');
    })
        conn.query('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);', 
            function (err, results, fields) {
                if (err) throw err;
        console.log('Created inventory table.');
    })
    conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['banana', 150], 
            function (err, results, fields) {
                if (err) throw err;
        else console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
    conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['orange', 154], 
            function (err, results, fields) {
                if (err) throw err;
        console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
    conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['apple', 100], 
    function (err, results, fields) {
                if (err) throw err;
        console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
    conn.end(function (err) { 
    if (err) throw err;
    else  console.log('Done.') 
    });
};
```

## <a name="read-data"></a>Adatok olvasása

A következő kóddal csatlakozhat, és beolvashatja az adatokat a **SELECT** SQL-utasítással. 

A [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) metódus a MySQL-kiszolgálóhoz való csatlakozásra szolgál. A [connect()](https://github.com/mysqljs/mysql#establishing-connections) metódus a kiszolgálóval való kapcsolat létesítésére szolgál. A [query()](https://github.com/mysqljs/mysql#performing-queries) metódus az SQL-lekérdezés MySQL-adatbázison való végrehajtására szolgál. Az eredménytömb a lekérdezés eredményeinek tárolására használható.

Cserélje le a `host`, `user`, `password` és `database` paramétert azokkal az értékekkel, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            readData();
        }
    });

function readData(){
    conn.query('SELECT * FROM inventory', 
        function (err, results, fields) {
            if (err) throw err;
            else console.log('Selected ' + results.length + ' row(s).');
            for (i = 0; i < results.length; i++) {
                console.log('Row: ' + JSON.stringify(results[i]));
            }
            console.log('Done.');
        })
    conn.end(
        function (err) { 
            if (err) throw err;
            else  console.log('Closing connection.') 
    });
};
```

## <a name="update-data"></a>Adatok frissítése

A következő kóddal csatlakozhat, és beolvashatja az adatokat az **UPDATE** SQL-utasítással. 

A [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) metódus a MySQL-kiszolgálóhoz való csatlakozásra szolgál. A [connect()](https://github.com/mysqljs/mysql#establishing-connections) metódus a kiszolgálóval való kapcsolat létesítésére szolgál. A [query()](https://github.com/mysqljs/mysql#performing-queries) metódus az SQL-lekérdezés MySQL-adatbázison való végrehajtására szolgál. 

Cserélje le a `host`, `user`, `password` és `database` paramétert azokkal az értékekkel, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            updateData();
        }
    });

function updateData(){
       conn.query('UPDATE inventory SET quantity = ? WHERE name = ?', [200, 'banana'], 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Updated ' + results.affectedRows + ' row(s).');
           })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Done.') 
        });
};
```

## <a name="delete-data"></a>Adat törlése

A következő kóddal csatlakozhat, és beolvashatja az adatokat a **DELETE** SQL-utasítással. 

A [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) metódus a MySQL-kiszolgálóhoz való csatlakozásra szolgál. A [connect()](https://github.com/mysqljs/mysql#establishing-connections) metódus a kiszolgálóval való kapcsolat létesítésére szolgál. A [query()](https://github.com/mysqljs/mysql#performing-queries) metódus az SQL-lekérdezés MySQL-adatbázison való végrehajtására szolgál. 

Cserélje le a `host`, `user`, `password` és `database` paramétert azokkal az értékekkel, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            deleteData();
        }
    });

function deleteData(){
       conn.query('DELETE FROM inventory WHERE name = ?', ['orange'], 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Deleted ' + results.affectedRows + ' row(s).');
           })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Done.') 
        });
};
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
> [Adatbázis migrálása exportálással és importálással](./concepts-migrate-import-export.md)
