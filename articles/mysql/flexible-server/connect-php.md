---
title: 'Gyors útmutató: kapcsolat a PHP-Azure Database for MySQL-rugalmas kiszolgáló használatával'
description: Ez a rövid útmutató számos PHP-kódrészletet tartalmaz, amelyekkel csatlakozhat, és lekérdezheti Azure Database for MySQL-rugalmas kiszolgáló adatait.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: dc6b069e3c7686ec6964dab890e503aa193cf6fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92545106"
---
# <a name="quickstart-use-php-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server"></a>Rövid útmutató: a PHP használatával csatlakozhat, és lekérdezheti az Azure Database for MySQL-rugalmas kiszolgálókon tárolt adatlekérdezéseket

> [!IMPORTANT]
> Azure Database for MySQL – a rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Ez a rövid útmutató bemutatja, hogyan csatlakozhat egy Azure Database for MySQL rugalmas kiszolgálóhoz egy [php](https://secure.php.net/manual/intro-whatis.php) -alkalmazás használatával. Bemutatjuk, hogy az SQL-utasítások használatával hogyan kérdezhetők le, illeszthetők be, frissíthetők és törölhetők az adatok az adatbázisban. Ez a cikk azt feltételezi, hogy már ismeri a PHP-vel való fejlesztést, és hogy új Azure Database for MySQL rugalmas kiszolgálóval való munkavégzéshez.

## <a name="prerequisites"></a>Előfeltételek
A rövid útmutató az alábbi útmutatók valamelyikében létrehozott erőforrásokat használja kiindulópontként:

- [Azure Database for MySQL rugalmas kiszolgáló létrehozása Azure Portal használatával](./quickstart-create-server-portal.md)
- [Azure Database for MySQL rugalmas kiszolgáló létrehozása az Azure CLI-vel](./quickstart-create-server-cli.md)

## <a name="preparing-your-client-workstation"></a>Az ügyfél munkaállomásának előkészítése
1. Ha a rugalmas kiszolgálót *privát hozzáféréssel (VNet-integrációval)* hozta létre, akkor a kiszolgálóval egy olyan erőforráshoz kell csatlakoznia, amely a kiszolgálóval megegyező VNet belül található. Létrehozhat egy virtuális gépet, és hozzáadhatja azt a rugalmas kiszolgálóval létrehozott VNet. Lásd: [Azure Database for MySQL rugalmas kiszolgálói virtuális hálózat létrehozása és kezelése az Azure CLI-](./how-to-manage-virtual-network-cli.md)vel.

2. Ha a rugalmas kiszolgálót *nyilvános hozzáféréssel (engedélyezett IP-címekkel)* hozta létre, akkor a helyi IP-címét felveheti a kiszolgálón található tűzfalszabályok listájára. Tekintse át [Azure Database for MySQL rugalmas kiszolgálói tűzfalszabályok létrehozása és kezelése az Azure CLI használatával](./how-to-manage-firewall-cli.md)című témakört.

### <a name="install-php"></a>A PHP telepítése

Telepítse a PHP-t a kiszolgálójára, vagy hozzon létre egy PHP-t tartalmazó Azure-[webalkalmazást](../../app-service/overview.md).  A Tűzfalszabályok létrehozásával [és kezelésével](./how-to-manage-firewall-portal.md) kapcsolatos információkért lásd: tűzfalszabályok létrehozása és kezelése.

#### <a name="macos"></a>macOS

1. Töltse le a [PHP 7.1.4-es verzióját](https://secure.php.net/downloads.php).
2. Telepítse a PHP-t, és további konfiguráláshoz tekintse meg a [php kézikönyvét](https://secure.php.net/manual/install.macosx.php) .

#### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. Töltse le a [php 7.1.4 nem biztonságos (x64) verzióját](https://secure.php.net/downloads.php).
2. Telepítse a PHP-t, és további konfiguráláshoz tekintse meg a [php kézikönyvét](https://secure.php.net/manual/install.unix.php) .

#### <a name="windows"></a>Windows

1. Töltse le a [php 7.1.4 nem biztonságos (x64) verzióját](https://windows.php.net/download#php-7.1).
2. Telepítse a PHP-t, és további konfiguráláshoz tekintse meg a [php kézikönyvét](https://secure.php.net/manual/install.windows.php) .

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése

Szerezze be a Azure Database for MySQL rugalmas kiszolgálóhoz való kapcsolódáshoz szükséges kapcsolati adatokat. Szüksége lesz a teljes kiszolgálónévre és a bejelentkezési hitelesítő adatokra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Azure Portal bal oldali menüjében válassza a **minden erőforrás** elemet, majd keresse meg a létrehozott kiszolgálót (például **mydemoserver**).
3. Válassza ki a kiszolgálónevet.
4. A kiszolgáló **Áttekintés** paneléről jegyezze fel a **Kiszolgálónevet** és a **Kiszolgáló-rendszergazdai bejelentkezési nevet**. Ha elfelejti a jelszavát, ezen a panelen új jelszót is tud kérni.
 <!---:::image type="content" source="./media/connect-php/1_server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::--->

## <a name="connecting-to-flexible-server-using-tlsssl-in-php"></a>Csatlakozás rugalmas kiszolgálóhoz TLS/SSL használatával PHP-ben

Ha az alkalmazásból TLS/SSL protokollon keresztül titkosított kapcsolatot szeretne létesíteni a rugalmas kiszolgálóval, tekintse meg az alábbi kódrészleteket. Letöltheti a TLS/SSL protokollon keresztüli kommunikációhoz szükséges tanúsítványt [https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)

```php using SSL
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootCA.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

## <a name="connect-and-create-a-table"></a>Csatlakozás és tábla létrehozása

A következő kód segítségével csatlakozzon, és hozzon létre egy táblát a **CREATE TABLE** SQL-utasítás használatával.

A kód a PHP beépített **MySQL Improved extension** (mysqli) osztályát használja. A kód meghívja a [mysqli_init](https://secure.php.net/manual/mysqli.init.php) és [mysqli_real_connect](https://secure.php.net/manual/mysqli.real-connect.php) metódust a MySQL-hez történő csatlakozáshoz. Ezután a kód meghívja [mysqli_query](https://secure.php.net/manual/mysqli.query.php) metódust a lekérdezés futtatásához. Végül pedig a [mysqli_close](https://secure.php.net/manual/mysqli.close.php) meghívásával bontja a kapcsolatot.

A host (gazdagép), a username (felhasználónév), a password (jelszó) és a db_name (adatbázisnév) paramétereket cserélje le a saját értékeire.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

// Run the create table query
if (mysqli_query($conn, '
CREATE TABLE Products (
`Id` INT NOT NULL AUTO_INCREMENT ,
`ProductName` VARCHAR(200) NOT NULL ,
`Color` VARCHAR(50) NOT NULL ,
`Price` DOUBLE NOT NULL ,
PRIMARY KEY (`Id`)
);
')) {
printf("Table created\n");
}

//Close the connection
mysqli_close($conn);
?>
```

## <a name="insert-data"></a>Adat beszúrása

Az alábbi kód használatával csatlakozhat, és adatokat szúrhat be egy **INSERT** SQL-utasítás segítségével.

A kód a PHP beépített **MySQL Improved extension** (mysqli) osztályát használja. A kód a [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) metódus használatával létrehoz egy előkészített beillesztési parancsot, majd a [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) metódus használatával hozzáköti az egyes beillesztett oszlopokhoz a paramétereket. A kód a [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) metódus használatával futtatja az utasítást, majd a [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) metódussal lezárja azt.

A host (gazdagép), a username (felhasználónév), a password (jelszó) és a db_name (adatbázisnév) paramétereket cserélje le a saját értékeire.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Create an Insert prepared statement and run it
$product_name = 'BrandNewProduct';
$product_color = 'Blue';
$product_price = 15.5;
if ($stmt = mysqli_prepare($conn, "INSERT INTO Products (ProductName, Color, Price) VALUES (?, ?, ?)")) {
mysqli_stmt_bind_param($stmt, 'ssd', $product_name, $product_color, $product_price);
mysqli_stmt_execute($stmt);
printf("Insert: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}

// Close the connection
mysqli_close($conn);
?>
```

## <a name="read-data"></a>Adatok olvasása

A következő kóddal csatlakozhat, és beolvashatja az adatokat a **SELECT** SQL-utasítással.  A kód a PHP beépített **MySQL Improved extension** (mysqli) osztályát használja. A kód a [mysqli_query](https://secure.php.net/manual/mysqli.query.php) metódus használatával végrehajtja az SQL-lekérdezést, és a [mysqli_fetch_assoc](https://secure.php.net/manual/mysqli-result.fetch-assoc.php) metódussal beolvassa az eredményül kapott sorokat.

A host (gazdagép), a username (felhasználónév), a password (jelszó) és a db_name (adatbázisnév) paramétereket cserélje le a saját értékeire.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Select query
printf("Reading data from table: \n");
$res = mysqli_query($conn, 'SELECT * FROM Products');
while ($row = mysqli_fetch_assoc($res)) {
var_dump($row);
}

//Close the connection
mysqli_close($conn);
?>
```

## <a name="update-data"></a>Adatok frissítése

Az alábbi kód használatával csatlakozhat és végezheti el az adatok módosítását egy **UPDATE** SQL-utasítás segítségével.

A kód a PHP beépített **MySQL Improved extension** (mysqli) osztályát használja. A kód a [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) metódus használatával létrehoz egy előkészített módosítási parancsot, majd a [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) metódus használatával hozzáköti az egyes módosított oszlopokhoz a paramétereket. A kód a [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) metódus használatával futtatja az utasítást, majd a [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) metódussal lezárja azt.

A host (gazdagép), a username (felhasználónév), a password (jelszó) és a db_name (adatbázisnév) paramétereket cserélje le a saját értékeire.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Update statement
$product_name = 'BrandNewProduct';
$new_product_price = 15.1;
if ($stmt = mysqli_prepare($conn, "UPDATE Products SET Price = ? WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 'ds', $new_product_price, $product_name);
mysqli_stmt_execute($stmt);
printf("Update: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));

//Close the connection
mysqli_stmt_close($stmt);
}

mysqli_close($conn);
?>
```


## <a name="delete-data"></a>Adat törlése
A következő kóddal csatlakozhat, és beolvashatja az adatokat a **DELETE** SQL-utasítással.

A kód a PHP beépített **MySQL Improved extension** (mysqli) osztályát használja. A kód a [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) metódus használatával létrehoz egy előkészített törlési parancsot, majd hozzáköti a paramétereket a parancs where záradékához a [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) metódus használatával. A kód a [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) metódus használatával futtatja az utasítást, majd a [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) metódussal lezárja azt.

A host (gazdagép), a username (felhasználónév), a password (jelszó) és a db_name (adatbázisnév) paramétereket cserélje le a saját értékeire.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Delete statement
$product_name = 'BrandNewProduct';
if ($stmt = mysqli_prepare($conn, "DELETE FROM Products WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 's', $product_name);
mysqli_stmt_execute($stmt);
printf("Delete: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}

//Close the connection
mysqli_close($conn);
?>
```
## <a name="next-steps"></a>Következő lépések
- [Titkosított kapcsolat Transport Layer Security (TLS 1,2) használatával Azure Database for MySQL-rugalmas kiszolgálón](./how-to-connect-tls-ssl.md).
- További információ a [Azure Database for MySQL rugalmas kiszolgáló hálózatkezeléséről](./concepts-networking.md).
- [Azure Database for MySQL rugalmas kiszolgálói tűzfalszabályok létrehozása és kezelése a Azure Portal használatával](./how-to-manage-firewall-portal.md).
- [Azure Database for MySQL rugalmas kiszolgálói virtuális hálózat létrehozása és kezelése Azure Portal használatával](./how-to-manage-virtual-network-portal.md).