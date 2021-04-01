---
title: 'Gyors útmutató: kapcsolat a PHP-Azure Database for MySQL használatával'
description: Ebben a gyors útmutatóban számos PHP-kódmintát biztosítunk a MySQL-hez készült Azure Database-ről való csatlakozáshoz és adatlekérdezéshez.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: bf62eca26d846cc529df43b519b1377f88c8aede
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98132902"
---
# <a name="quickstart-use-php-to-connect-and-query-data-in-azure-database-for-mysql"></a>Rövid útmutató: a PHP használatával csatlakozhat és lekérdezheti Azure Database for MySQL
Ebben a gyors útmutatóban azt szemléltetjük, hogy miként lehet [PHP](https://secure.php.net/manual/intro-whatis.php)-alkalmazás használatával csatlakozni a MySQL-hez készült Azure Database-hez. Bemutatjuk, hogy az SQL-utasítások használatával hogyan kérdezhetők le, illeszthetők be, frissíthetők és törölhetők az adatok az adatbázisban.

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

- [Adatbázis és nem rendszergazda felhasználó létrehozása](./howto-create-users.md?tabs=single-server)
- Az operációs rendszer legújabb PHP-verziójának telepítése
    - [PHP macOS rendszeren](https://secure.php.net/manual/install.macosx.php)
    - [PHP Linuxon](https://secure.php.net/manual/install.unix.php)
    - [PHP Windowson](https://secure.php.net/manual/install.windows.php)

> [!NOTE]
> Ebben a rövid útmutatóban a [mysqli](https://www.php.net/manual/en/book.mysqli.php) könyvtárat használjuk a kapcsolódás kezeléséhez és a kiszolgáló lekérdezéséhez.

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése
Az adatbázis-kiszolgáló csatlakoztatási információit a Azure Portal a következő lépésekkel kérheti le:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/).
2. Navigáljon a MySQL-hez készült Azure-adatbázisok oldalára. Megkeresheti és kiválaszthatja a **Azure Database for MySQL**.
:::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="Azure Database for MySQL keresése":::

2. Válassza ki a MySQL-kiszolgálót (például **mydemoserver**).
3. Az **Áttekintés** lapon másolja a teljes **kiszolgálónevet és a** rendszergazdai felhasználónevet a **kiszolgáló-rendszergazdai bejelentkezési név** mellett. A kiszolgálónév vagy az állomásnév másolásához vigye a kurzort a fölé, és válassza a **Másolás** ikont.

> [!IMPORTANT]
> - Ha elfelejtette a jelszavát, [alaphelyzetbe állíthatja a jelszót](./howto-create-manage-server-portal.md#update-admin-password).
> - Cserélje le a **gazdagépet, a felhasználónevet, a jelszót** és a **db_name** paramétereket a saját értékekkel * *

## <a name="step-1-connect-to-the-server"></a>1. lépés: Kapcsolódás a kiszolgálóhoz
Az SSL alapértelmezés szerint engedélyezve van. Előfordulhat, hogy le kell töltenie a [DIGICERTGLOBALROOTG2 SSL-tanúsítványt](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) a helyi környezetből való kapcsolódáshoz. A kód hívása:
- [mysqli_init](https://secure.php.net/manual/mysqli.init.php) a mysqli inicializálásához.
- [mysqli_ssl_set](https://www.php.net/manual/en/mysqli.ssl-set.php) , hogy az SSL-tanúsítvány elérési útjára mutasson. Ez a helyi környezethez szükséges, de App Service webalkalmazáshoz vagy Azure-beli virtuális gépekhez nem szükséges.
- [mysqli_real_connect](https://secure.php.net/manual/mysqli.real-connect.php) a MySQL-hez való kapcsolódáshoz.
- [mysqli_close](https://secure.php.net/manual/mysqli.close.php) a kapcsolódás lezárásához.


```php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Initializes MySQLi
$conn = mysqli_init();

mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootG2.crt.pem", NULL, NULL);

// Establish the connection
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, NULL, MYSQLI_CLIENT_SSL);

//If connection failed, show the error
if (mysqli_connect_errno($conn))
{
    die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
[Problémák léptek fel? Tudassa velünk](https://aka.ms/mysql-doc-feedback)

## <a name="step-2-create-a-table"></a>2. lépés: tábla létrehozása
A kapcsolódáshoz használja a következő kódot. A kód hívása:
- [mysqli_query](https://secure.php.net/manual/mysqli.query.php) a lekérdezés futtatásához.
```php
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
```

## <a name="step-3-insert-data"></a>3. lépés: az adatbeszúrás
Az alábbi kód használatával szúrhat be **adatok beszúrására szolgáló SQL-** utasítás használatával. Ez a kód a metódusokat használja:
- [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) előkészített INSERT utasítás létrehozásához
- [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) a paraméterek kötését az egyes beszúrt oszlopok értékeihez.
- [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php)
- [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) az utasítás bezárásához metódus használatával


```php
//Create an Insert prepared statement and run it
$product_name = 'BrandNewProduct';
$product_color = 'Blue';
$product_price = 15.5;
if ($stmt = mysqli_prepare($conn, "INSERT INTO Products (ProductName, Color, Price) VALUES (?, ?, ?)"))
{
    mysqli_stmt_bind_param($stmt, 'ssd', $product_name, $product_color, $product_price);
    mysqli_stmt_execute($stmt);
    printf("Insert: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
    mysqli_stmt_close($stmt);
}

```

## <a name="step-4-read-data"></a>4. lépés: az adatolvasás
A következő kód használatával olvashatja el az adatok egy **Select** SQL-utasítás használatával.  A kód a metódust használja:
- [mysqli_query](https://secure.php.net/manual/mysqli.query.php) a **Select** lekérdezés végrehajtása
- [mysqli_fetch_assoc](https://secure.php.net/manual/mysqli-result.fetch-assoc.php) az eredményül kapott sorok beolvasásához.

```php
//Run the Select query
printf("Reading data from table: \n");
$res = mysqli_query($conn, 'SELECT * FROM Products');
while ($row = mysqli_fetch_assoc($res))
 {
    var_dump($row);
 }

```


## <a name="step-5-delete-data"></a>5. lépés: az adattörlés
A következő kód használatával törölheti a sorokat egy **delete** SQL-utasítás használatával. A kód a metódusokat használja:
- előkészített törlési utasítás létrehozásához [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php)
- [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) a paraméterek kötését
- [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) végrehajtja az előkészített delete utasítást
- [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) bezárja az utasítást

```php
//Run the Delete statement
$product_name = 'BrandNewProduct';
if ($stmt = mysqli_prepare($conn, "DELETE FROM Products WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 's', $product_name);
mysqli_stmt_execute($stmt);
printf("Delete: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
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
> [Azure Database for MySQL-kiszolgáló kezelése a portál használatával](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Azure Database for MySQL-kiszolgáló kezelése a parancssori felület használatával](./how-to-manage-single-server-cli.md)

[Nem találja, amit keres? Tudassa velünk.](https://aka.ms/mysql-doc-feedback)
