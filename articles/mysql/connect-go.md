---
title: 'Gyors útmutató: kapcsolat a go-Azure Database for MySQL használatával'
description: Ez a rövid útmutató több Go-mintakódot biztosít, amelyekkel csatlakozhat a MySQL-hez készült Azure-adatbázishoz, illetve adatokat kérdezhet le róla.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.devlang: go
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: 0da32fb404972556a1c4c964eec692bad20a4db1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94535758"
---
# <a name="quickstart-use-go-language-to-connect-and-query-data-in-azure-database-for-mysql"></a>Gyors útmutató: kapcsolódás és adatlekérdezés a go Language használatával Azure Database for MySQL

Ez a rövid útmutató azt ismerteti, hogyan lehet csatlakozni az Azure Database for MySQL-hez Windows, Ubuntu Linux és Apple macOS platformról [Go](https://golang.org/) nyelven írt kóddal. Bemutatjuk, hogy az SQL-utasítások használatával hogyan kérdezhetők le, illeszthetők be, frissíthetők és törölhetők az adatok az adatbázisban. Ez a témakör azt feltételezi, hogy Ön a Go használata terén rendelkezik fejlesztési tapasztalatokkal, de az Azure Database for MySQL használatában még járatlan.

## <a name="prerequisites"></a>Előfeltételek
A rövid útmutató az alábbi útmutatók valamelyikében létrehozott erőforrásokat használja kiindulópontként:
- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure Portal használatával](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure CLI használatával](./quickstart-create-mysql-server-database-using-azure-cli.md)

> [!IMPORTANT] 
> Győződjön meg arról, hogy az IP-cím, amelyhez csatlakozik, a [Azure Portal](./howto-manage-firewall-using-portal.md) vagy az [Azure CLI](./howto-manage-firewall-using-cli.md) használatával adja hozzá a kiszolgáló tűzfalszabály-szabályait.

## <a name="install-go-and-mysql-connector"></a>A Go és a MySQL-összekötő telepítése
Telepítse a [Gót](https://golang.org/doc/install) és a [go-sql-driver for MySQL](https://github.com/go-sql-driver/mysql#installation) illesztőt a saját számítógépére. Kövesse a platformjának megfelelő lépéseket:

### <a name="windows"></a>Windows
1. [Töltse le](https://golang.org/dl/) és telepítse a Microsoft Windowshoz készült Go-t a [telepítési utasítások](https://golang.org/doc/install) szerint.
2. Nyissa meg a parancssort a Start menüből.
3. Hozzon létre egy mappát a projekt számára, például `mkdir  %USERPROFILE%\go\src\mysqlgo`.
4. Nyissa meg a projektmappát (például `cd %USERPROFILE%\go\src\mysqlgo`).
5. Úgy állítsa be a GOPATH környezeti változóját, hogy a forráskód könyvtárára mutasson. `set GOPATH=%USERPROFILE%\go`.
6. Telepítse a [go-sql-driver for mysql](https://github.com/go-sql-driver/mysql#installation) illesztőt a `go get github.com/go-sql-driver/mysql` parancs futtatásával.

   Összefoglalva, telepítse a Go-t, majd futtassa ezeket a parancsokat a parancssorban:
   ```cmd
   mkdir  %USERPROFILE%\go\src\mysqlgo
   cd %USERPROFILE%\go\src\mysqlgo
   set GOPATH=%USERPROFILE%\go
   go get github.com/go-sql-driver/mysql
   ```

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. Indítsa el a Bash felületet. 
2. Telepítse a Go-t a `sudo apt-get install golang-go` parancs futtatásával.
3. Hozzon létre egy mappát a projekt számára a kezdőkönyvtárban (például `mkdir -p ~/go/src/mysqlgo/`).
4. Nyissa meg a projektmappát (például `cd ~/go/src/mysqlgo/`).
5. Úgy állítsa be a GOPATH környezeti változót, hogy egy érvényes forráskönyvtárra mutasson, például az aktuális kezdőkönyvtár Go mappájára. A Bash felületen futtassa az `export GOPATH=~/go` parancsot, amellyel a go könyvtárat GOPATH útvonalként adhatja meg az aktuális felületi munkamenethez.
6. Telepítse a [go-sql-driver for mysql](https://github.com/go-sql-driver/mysql#installation) illesztőt a `go get github.com/go-sql-driver/mysql` parancs futtatásával.

   Összefoglalva, futtassa ezeket a bash-parancsokat:
   ```bash
   sudo apt-get install golang-go
   mkdir -p ~/go/src/mysqlgo/
   cd ~/go/src/mysqlgo/
   export GOPATH=~/go/
   go get github.com/go-sql-driver/mysql
   ```

### <a name="apple-macos"></a>Apple macOS
1. Töltse le és telepítse a Go-t a platformnak megfelelő [telepítési utasítások](https://golang.org/doc/install) szerint. 
2. Indítsa el a Bash felületet.
3. Hozzon létre egy mappát a projekt számára a kezdőkönyvtárban (például `mkdir -p ~/go/src/mysqlgo/`).
4. Nyissa meg a projektmappát (például `cd ~/go/src/mysqlgo/`).
5. Úgy állítsa be a GOPATH környezeti változót, hogy egy érvényes forráskönyvtárra mutasson, például az aktuális kezdőkönyvtár Go mappájára. A Bash felületen futtassa az `export GOPATH=~/go` parancsot, amellyel a go könyvtárat GOPATH útvonalként adhatja meg az aktuális felületi munkamenethez.
6. Telepítse a [go-sql-driver for mysql](https://github.com/go-sql-driver/mysql#installation) illesztőt a `go get github.com/go-sql-driver/mysql` parancs futtatásával.

   Összefoglalva, telepítse a Go-t, majd futtassa ezeket a bash-parancsokat:
   ```bash
   mkdir -p ~/go/src/mysqlgo/
   cd ~/go/src/mysqlgo/
   export GOPATH=~/go/
   go get github.com/go-sql-driver/mysql
   ```

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése
Kérje le a MySQL-hez készült Azure Database-hez való csatlakozáshoz szükséges kapcsolatadatokat. Szüksége lesz a teljes kiszolgálónévre és a bejelentkezési hitelesítő adatokra.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/).
2. Az Azure Portal bal oldali menüjében kattintson a **Minden erőforrás** lehetőségre, és keressen rá a létrehozott kiszolgálóra (például **mydemoserver**).
3. Kattintson a kiszolgálónévre.
4. A kiszolgáló **Áttekintés** paneléről jegyezze fel a **Kiszolgálónevet** és a **Kiszolgáló-rendszergazdai bejelentkezési nevet**. Ha elfelejti a jelszavát, ezen a panelen új jelszót is tud kérni.
 :::image type="content" source="./media/connect-go/1_server-overview-name-login.png" alt-text="A MySQL-hez készült Azure Database-kiszolgáló neve":::
   

## <a name="build-and-run-go-code"></a>Go kód felépítése és futtatása 
1. Golang-kód írásához használhat egy egyszerű szövegszerkesztőt, ilyen például Microsoft Windows rendszeren a Jegyzettömb, Ubuntu rendszeren a [vi](https://manpages.ubuntu.com/manpages/xenial/man1/nvi.1.html#contenttoc5) vagy a [Nano](https://www.nano-editor.org/), macOS rendszeren pedig a TextEdit. Ha szeretne egy gazdagabb interaktív fejlesztési környezetet (IDE), próbálja ki a [Gogland](https://www.jetbrains.com/go/) by JetBrains, a [Visual Studio Code](https://code.visualstudio.com/) by Microsoft vagy az [Atom](https://atom.io/).
2. Az alábbi szakaszokban található Go-kódokat illessze be szövegfájlokba, majd mentse a fájlokat a projektmappába \*.go kiterjesztéssel, például `%USERPROFILE%\go\src\mysqlgo\createtable.go` (Windows) vagy `~/go/src/mysqlgo/createtable.go` (Linux) elérési úton.
3. Keresse meg a `HOST`, a `DATABASE`, a `USER` és a `PASSWORD` állandót a kódban, és a példaértékeket cserélje le a saját értékeire. 
4. Nyissa meg a parancssort vagy a Bash felületet. Lépjen a projektmappára. Windows rendszer például a következővel: `cd %USERPROFILE%\go\src\mysqlgo\`. Linuxon: `cd ~/go/src/mysqlgo/`.  A fentiekben említettek közül egyes IDE-szerkesztők hibakeresési és futásidejű képességeket biztosítanak anélkül, hogy rendszerhéjparancsokra lenne szükség.
5. A kód futtatásához írja be a `go run createtable.go` parancsot az alkalmazás lefordításához és futtatásához. 
6. Vagy a kód natív alkalmazásba való beépítéséhez írja be a `go build createtable.go` parancsot, majd indítsa el a `createtable.exe` fájlt az alkalmazás futtatásához.

## <a name="connect-create-table-and-insert-data"></a>Csatlakozás, táblák létrehozása és adatok beszúrása
Az alábbi kód használatával csatlakozhat a kiszolgálóhoz, létrehozhat egy táblát és betöltheti az adatokat egy **INSERT** SQL-utasítással. 

A kód három csomagot importál: az [sql package](https://golang.org/pkg/database/sql/) és a [go sql driver for mysql](https://github.com/go-sql-driver/mysql#installation) csomagot illesztőként a MySQL-hez készült Azure adatbázissal való kommunikációhoz, illetve az [fmt package](https://golang.org/pkg/fmt/) csomagot a nyomtatott bemenetekhez és kimenetekhez a parancssoron.

A kód meghívja az [sql.Open()](http://go-database-sql.org/accessing.html) metódust az Azure Database for MySQL-hez való csatlakozáshoz, majd a [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping) metódussal ellenőrzi a kapcsolatot. A rendszer a folyamat során [adatbázis-leírót](https://golang.org/pkg/database/sql/#DB) használ, amely az adatbázis-kiszolgáló kapcsolatkészletét tárolja. A kód többször is meghívja az [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) metódust, hogy különböző DDL-parancsokat futtasson. A kód a [Prepare()](http://go-database-sql.org/prepared.html) és az Exec() metódussal előkészített utasításokat is futtat különböző paraméterekkel három sor beszúrásához. A rendszer minden alkalommal egyéni checkError() metódust használ a hibák ellenőrzéséhez és a kilépéshez.

Cserélje le a `host`, `database`, `user` és `password` állandókat a saját értékeire. 

```Go
package main

import (
    "database/sql"
    "fmt"

    _ "github.com/go-sql-driver/mysql"
)

const (
    host     = "mydemoserver.mysql.database.azure.com"
    database = "quickstartdb"
    user     = "myadmin@mydemoserver"
    password = "yourpassword"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true", user, password, host, database)

    // Initialize connection object.
    db, err := sql.Open("mysql", connectionString)
    checkError(err)
    defer db.Close()

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database.")

    // Drop previous table of same name if one exists.
    _, err = db.Exec("DROP TABLE IF EXISTS inventory;")
    checkError(err)
    fmt.Println("Finished dropping table (if existed).")

    // Create table.
    _, err = db.Exec("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
    checkError(err)
    fmt.Println("Finished creating table.")

    // Insert some data into table.
    sqlStatement, err := db.Prepare("INSERT INTO inventory (name, quantity) VALUES (?, ?);")
    res, err := sqlStatement.Exec("banana", 150)
    checkError(err)
    rowCount, err := res.RowsAffected()
    fmt.Printf("Inserted %d row(s) of data.\n", rowCount)

    res, err = sqlStatement.Exec("orange", 154)
    checkError(err)
    rowCount, err = res.RowsAffected()
    fmt.Printf("Inserted %d row(s) of data.\n", rowCount)

    res, err = sqlStatement.Exec("apple", 100)
    checkError(err)
    rowCount, err = res.RowsAffected()
    fmt.Printf("Inserted %d row(s) of data.\n", rowCount)
    fmt.Println("Done.")
}

```

## <a name="read-data"></a>Adatok olvasása
A következő kóddal csatlakozhat, és beolvashatja az adatokat a **SELECT** SQL-utasítással. 

A kód három csomagot importál: az [sql package](https://golang.org/pkg/database/sql/) és a [go sql driver for mysql](https://github.com/go-sql-driver/mysql#installation) csomagot illesztőként a MySQL-hez készült Azure adatbázissal való kommunikációhoz, illetve az [fmt package](https://golang.org/pkg/fmt/) csomagot a nyomtatott bemenetekhez és kimenetekhez a parancssoron.

A kód meghívja az [sql.Open()](http://go-database-sql.org/accessing.html) metódust a MySQL-hez készült Azure-adatbázishoz való csatlakozáshoz, majd a [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping) metódussal ellenőrzi a kapcsolatot. A rendszer a folyamat során [adatbázis-leírót](https://golang.org/pkg/database/sql/#DB) használ, amely az adatbázis-kiszolgáló kapcsolatkészletét tárolja. A kód meghívja a [Query()](https://golang.org/pkg/database/sql/#DB.Query) metódust a kiválasztott parancs futtatásához. Ezután a [Next()](https://golang.org/pkg/database/sql/#Rows.Next) metódust futtatja az eredmények közötti iterációhoz, a [Scan()](https://golang.org/pkg/database/sql/#Rows.Scan) használatával elemzi az oszlopértékeket, és menti az értéket a változókba. A rendszer minden alkalommal egyéni checkError() metódust használ a hibák ellenőrzéséhez és a kilépéshez.

Cserélje le a `host`, `database`, `user` és `password` állandókat a saját értékeire. 

```Go
package main

import (
    "database/sql"
    "fmt"

    _ "github.com/go-sql-driver/mysql"
)

const (
    host     = "mydemoserver.mysql.database.azure.com"
    database = "quickstartdb"
    user     = "myadmin@mydemoserver"
    password = "yourpassword"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true", user, password, host, database)

    // Initialize connection object.
    db, err := sql.Open("mysql", connectionString)
    checkError(err)
    defer db.Close()

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database.")

    // Variables for printing column data when scanned.
    var (
        id       int
        name     string
        quantity int
    )

    // Read some data from the table.
    rows, err := db.Query("SELECT id, name, quantity from inventory;")
    checkError(err)
    defer rows.Close()
    fmt.Println("Reading data:")
    for rows.Next() {
        err := rows.Scan(&id, &name, &quantity)
        checkError(err)
        fmt.Printf("Data row = (%d, %s, %d)\n", id, name, quantity)
    }
    err = rows.Err()
    checkError(err)
    fmt.Println("Done.")
}
```

## <a name="update-data"></a>Adatok frissítése
A következő kód használatával csatlakozhat, és frissítheti az adatokat az **UPDATE** SQL-utasítással. 

A kód három csomagot importál: az [sql package](https://golang.org/pkg/database/sql/) és a [go sql driver for mysql](https://github.com/go-sql-driver/mysql#installation) csomagot illesztőként a MySQL-hez készült Azure adatbázissal való kommunikációhoz, illetve az [fmt package](https://golang.org/pkg/fmt/) csomagot a nyomtatott bemenetekhez és kimenetekhez a parancssoron.

A kód meghívja az [sql.Open()](http://go-database-sql.org/accessing.html) metódust a MySQL-hez készült Azure-adatbázishoz való csatlakozáshoz, majd a [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping) metódussal ellenőrzi a kapcsolatot. A rendszer a folyamat során [adatbázis-leírót](https://golang.org/pkg/database/sql/#DB) használ, amely az adatbázis-kiszolgáló kapcsolatkészletét tárolja. A kód meghívja az [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) metódust a frissítési parancs futtatásához. A rendszer minden alkalommal egyéni checkError() metódust használ a hibák ellenőrzéséhez és a kilépéshez.

Cserélje le a `host`, `database`, `user` és `password` állandókat a saját értékeire. 

```Go
package main

import (
    "database/sql"
    "fmt"

    _ "github.com/go-sql-driver/mysql"
)

const (
    host     = "mydemoserver.mysql.database.azure.com"
    database = "quickstartdb"
    user     = "myadmin@mydemoserver"
    password = "yourpassword"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true", user, password, host, database)

    // Initialize connection object.
    db, err := sql.Open("mysql", connectionString)
    checkError(err)
    defer db.Close()

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database.")

    // Modify some data in table.
    rows, err := db.Exec("UPDATE inventory SET quantity = ? WHERE name = ?", 200, "banana")
    checkError(err)
    rowCount, err := rows.RowsAffected()
    fmt.Printf("Updated %d row(s) of data.\n", rowCount)
    fmt.Println("Done.")
}
```

## <a name="delete-data"></a>Adat törlése
A következő kód használatával csatlakozhat, és eltávolíthatja az adatokat a **DELETE** SQL-utasítással. 

A kód három csomagot importál: az [sql package](https://golang.org/pkg/database/sql/) és a [go sql driver for mysql](https://github.com/go-sql-driver/mysql#installation) csomagot illesztőként a MySQL-hez készült Azure adatbázissal való kommunikációhoz, illetve az [fmt package](https://golang.org/pkg/fmt/) csomagot a nyomtatott bemenetekhez és kimenetekhez a parancssoron.

A kód meghívja az [sql.Open()](http://go-database-sql.org/accessing.html) metódust a MySQL-hez készült Azure-adatbázishoz való csatlakozáshoz, majd a [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping) metódussal ellenőrzi a kapcsolatot. A rendszer a folyamat során [adatbázis-leírót](https://golang.org/pkg/database/sql/#DB) használ, amely az adatbázis-kiszolgáló kapcsolatkészletét tárolja. A kód meghívja az [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) metódust a törlési parancs futtatásához. A rendszer minden alkalommal egyéni checkError() metódust használ a hibák ellenőrzéséhez és a kilépéshez.

Cserélje le a `host`, `database`, `user` és `password` állandókat a saját értékeire. 

```Go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/go-sql-driver/mysql"
)

const (
    host     = "mydemoserver.mysql.database.azure.com"
    database = "quickstartdb"
    user     = "myadmin@mydemoserver"
    password = "yourpassword"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true", user, password, host, database)

    // Initialize connection object.
    db, err := sql.Open("mysql", connectionString)
    checkError(err)
    defer db.Close()

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database.")

    // Modify some data in table.
    rows, err := db.Exec("DELETE FROM inventory WHERE name = ?", "orange")
    checkError(err)
    rowCount, err := rows.RowsAffected()
    fmt.Printf("Deleted %d row(s) of data.\n", rowCount)
    fmt.Println("Done.")
}
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
