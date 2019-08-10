---
title: Csatlakozás az Azure Database for MySQL-hez a Node.js használatával
description: Ez a rövid útmutató számos Node.js-mintakódot biztosít, amelyekkel csatlakozhat a MySQL-hez készült Azure-adatbázishoz, illetve adatokat kérdezhet le róla.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/21/2018
ms.openlocfilehash: ad022f6ac9cebbe92cdca3a4b368524d828a9cbb
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68931562"
---
# <a name="azure-database-for-mysql-use-nodejs-to-connect-and-query-data"></a>Azure Database for MySQL: A Node. js használata a kapcsolódáshoz és az adatlekérdezéshez
Ez a rövid útmutató ismerteti, hogyan használható a [Node.js](https://nodejs.org/) a MySQL-hez készült Azure-adatbázishoz való csatlakozáshoz Windows, Ubuntu Linux és Mac platformról. Azt is bemutatja, hogyan lehet SQL-utasítások használatával adatokat lekérdezni, beszúrni, frissíteni és törölni az adatbázisban. Ez a témakör azt feltételezi, hogy a Node.js használata terén rendelkezik fejlesztési tapasztalatokkal, de az Azure Database for MySQL használatában még járatlan.

## <a name="prerequisites"></a>Előfeltételek
Ebben a rövid útmutatóban a következő útmutatók valamelyikében létrehozott erőforrásokat használunk kiindulási pontként:
- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure Portal használatával](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure CLI használatával](./quickstart-create-mysql-server-database-using-azure-cli.md)

Emellett a következőket kell elvégezni:
- Telepítse a [Node.js](https://nodejs.org)-futtatókörnyezetet.
- Telepítse a [MySQL](https://www.npmjs.com/package/mysql) -csomagot a MySQL-hez a Node. js-alkalmazásból való kapcsolódáshoz. 

## <a name="install-nodejs-and-the-mysql-connector"></a>A Node.js és a MySQL-összekötő telepítése
A Node.js telepítéséhez kövesse a megfelelő szakaszban leírt utasításokat a platformtól függően. A NPM használatával telepítse a MySQL-csomagot és annak függőségeit a projekt mappájába.

### <a name="windows"></a>**Windows**
1. Látogasson el a [Node.js letöltési oldalára](https://nodejs.org/en/download/), majd válassza ki a kívánt Windows-telepítési lehetőséget.
2. Hozzon létre egy helyi projektmappát, például: `nodejsmysql`. 
3. Nyissa meg a parancssort, majd módosítsa a könyvtárat a Project mappába, például`cd c:\nodejsmysql\`
4. A NPM eszköz futtatásával telepítse a MySQL-függvénytárat a Project mappába.

   ```cmd
   cd c:\nodejsmysql\
   "C:\Program Files\nodejs\npm" install mysql
   "C:\Program Files\nodejs\npm" list
   ```

5. Ellenőrizze a telepítést a `npm list` kimeneti szöveg ellenőrzésével. Új javítások kiadásakor a verziószám változhat.

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
1. A **Node.js** és az **npm**, a Node.js csomagkezelőjének telepítéséhez futtassa a következő parancsokat.

   ```bash
   sudo apt-get install -y nodejs npm
   ```

2. Futtassa a következő parancsokat egy projekt mappájának `mysqlnodejs` létrehozásához, és telepítse a MySQL-csomagot a mappába.

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql
   npm list
   ```
3. Ellenőrizze a telepítést a NPM-lista kimeneti szövegének ellenőrzésével. Új javítások kiadásakor a verziószám változhat.

### <a name="mac-os"></a>**Mac OS**
1. A **brew**, egy Mac OS X-szel és a **Node.js**-vel kompatibilis, egyszerűen használható csomagkezelő telepítéséhez adja meg a következő parancsokat.

   ```bash
   ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
   brew install node
   ```
2. Futtassa a következő parancsokat egy projekt mappájának `mysqlnodejs` létrehozásához, és telepítse a MySQL-csomagot a mappába.

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql
   npm list
   ```

3. Ellenőrizze a telepítést a `npm list` kimeneti szöveg ellenőrzésével. Új javítások kiadásakor a verziószám változhat.

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése
Kérje le a MySQL-hez készült Azure Database-hez való csatlakozáshoz szükséges kapcsolatadatokat. Ehhez szükség lesz a teljes kiszolgálónévre és bejelentkezési hitelesítő adatokra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Azure Portal bal oldali menüjében válassza a **minden erőforrás**elemet, majd keresse meg a létrehozott kiszolgálót (például **mydemoserver**).
3. Válassza ki a kiszolgálónevet.
4. A kiszolgáló **Áttekintés** paneléről jegyezze fel a **Kiszolgálónevet** és a **Kiszolgáló-rendszergazdai bejelentkezési nevet**. Ha elfelejti a jelszavát, ezen a panelen új jelszót is tud kérni.
 ![A MySQL-hez készült Azure Database-kiszolgáló neve](./media/connect-nodejs/1_server-overview-name-login.png)

## <a name="running-the-javascript-code-in-nodejs"></a>A JavaScript-kód futtatása a Node.js-ben
1. Illessze be a JavaScript-kódot szövegfájlokba, és mentse őket egy projektmappába .js kiterjesztéssel (például: C:\nodejsmysql\createtable.js vagy /home/username/nodejsmysql/createtable.js).
2. Nyissa meg a parancssort vagy a bash rendszerhéjt, majd módosítsa a könyvtárat `cd nodejsmysql`a projekt mappájába.
3. Az alkalmazás futtatásához írja be a Node parancsot, majd a fájlnevet, például `node createtable.js`:.
4. Ha Windows rendszeren nem található a Node.js-alkalmazás a path környezeti változóban, előfordulhat, hogy a teljes elérési utat kell használnia a Node.js-alkalmazás elindításához, például: `"C:\Program Files\nodejs\node.exe" createtable.js`

## <a name="connect-create-table-and-insert-data"></a>Csatlakozás, táblák létrehozása és adatok beszúrása
A következő kóddal csatlakozhat, és betöltheti az adatokat a **CREATE TABLE** és az **INSERT INTO** SQL-utasításokkal.

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

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálással és importálással](./concepts-migrate-import-export.md)
