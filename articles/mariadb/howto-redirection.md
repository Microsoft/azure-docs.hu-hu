---
title: Kapcsolat átirányítással – Azure Database for MariaDB
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja az alkalmazást, hogy a Azure Database for MariaDBhoz kapcsolódjon az átirányítás használatával.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/8/2020
ms.openlocfilehash: 3f26de72839fcaa39bff4d827aba757721736934
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664902"
---
# <a name="connect-to-azure-database-for-mariadb-with-redirection"></a>Kapcsolódás Azure Database for MariaDBhoz átirányítás használatával

Ez a témakör azt ismerteti, hogyan lehet egy alkalmazást Azure Database for MariaDB-kiszolgálót átirányítási móddal összekapcsolni. Az átirányítás célja az ügyfélalkalmazások és a MariaDB-kiszolgálók közötti hálózati késés csökkentése azáltal, hogy az alkalmazások közvetlenül a háttérbeli kiszolgáló csomópontjaihoz csatlakoznak.

## <a name="before-you-begin"></a>Előkészületek
Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Hozzon létre egy Azure Database for MariaDB-kiszolgálót a 10,2-es vagy a 10,3-es verziójú motorral. 

Részletekért lásd: Azure Database for MariaDB kiszolgáló létrehozása a [Azure Portal](quickstart-create-mariadb-server-database-using-azure-portal.md) vagy az [Azure CLI](quickstart-create-mariadb-server-database-using-azure-cli.md)használatával.

## <a name="enable-redirection"></a>Átirányítás engedélyezése

A Azure Database for MariaDB-kiszolgálón konfigurálja a paramétert úgy, hogy `redirect_enabled` `ON` engedélyezze a kapcsolatokat az átirányítási móddal. A kiszolgálói paraméter frissítéséhez használja a [Azure Portal](howto-server-parameters.md) vagy az [Azure CLI](howto-configure-server-parameters-cli.md)-t.

## <a name="php"></a>PHP

A PHP-alkalmazások átirányításának támogatása a Microsoft által fejlesztett [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) -bővítményen keresztül érhető el. 

A mysqlnd_azure bővítmény elérhető a PHP-alkalmazások számára a PECL-n keresztül, és erősen ajánlott a bővítmény telepítése és konfigurálása a hivatalosan közzétett [PECL-csomagon](https://pecl.php.net/package/mysqlnd_azure)keresztül.

> [!IMPORTANT]
> Az átirányítás támogatása a PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) bővítményben jelenleg előzetes verzióban érhető el.

### <a name="redirection-logic"></a>Átirányítási logika

>[!IMPORTANT]
> Az átirányítási logika/viselkedés elején lévő 1.1.0-es verziójának frissítése megtörtént, és a **1.1.0 + használata javasolt**.

Az átirányítási viselkedést a értéke határozza meg `mysqlnd_azure.enableRedirect` . Az alábbi táblázat az átirányítás viselkedését ismerteti a paraméternek a **1.1.0 + verziótól** kezdődő értéke alapján.

Ha a mysqlnd_azure bővítmény (1.0.0-1.0.3 verzió) régebbi verzióját használja, az átirányítási viselkedést a értéke határozza meg `mysqlnd_azure.enabled` . Az érvényes értékek `off` (az alábbi táblázatban szereplő viselkedéssel hasonlóan viselkednek), és `on` (az alábbi táblázat szerint viselkednek `preferred` ).  

|**mysqlnd_azure. enableRedirect érték**| **Működés**|
|----------------------------------------|-------------|
|`off` vagy `0`|Az átirányítás nem lesz használatban. |
|`on` vagy `1`|– Ha a kapcsolat nem használ SSL-t az illesztőprogram oldalán, nem történik kapcsolat. A rendszer a következő hibaüzenetet adja vissza: *"mysqlnd_azure. enableRedirect, de az SSL-beállítás nincs beállítva a kapcsolati karakterláncban. Az átirányítás csak SSL használatával lehetséges. "*<br>-Ha az SSL használatban van az illesztőprogram oldalán, de az átirányítás nem támogatott a kiszolgálón, az első kapcsolat megszakad, és a rendszer a következő hibaüzenetet adja vissza: *"a kapcsolat megszakadt, mert az átirányítás nincs engedélyezve a MariaDB-kiszolgálón, vagy a hálózati csomag nem felel meg az átirányítási protokollnak."*<br>-Ha a MariaDB-kiszolgáló támogatja az átirányítás használatát, de az átirányított csatlakozás valamilyen okból meghiúsult, akkor az első proxy-csatlakozást is megszakítja. Az átirányított kapcsolatok hibájának visszaadása.|
|`preferred` vagy `2`<br> (alapértelmezett érték)|– Ha lehetséges, a mysqlnd_azure átirányítást fog használni.<br>– Ha a kapcsolat nem használ SSL-t az illesztőprogram oldalán, a kiszolgáló nem támogatja az átirányítást, vagy az átirányított kapcsolat nem tud csatlakozni a nem végzetes okból, amíg a proxy kapcsolata még érvényes, akkor az az első proxy-kapcsolathoz fog visszatérni.|

A dokumentum következő fejezetei azt ismertetik, hogyan kell telepíteni a `mysqlnd_azure` bővítményt a PECL használatával, és beállítani a paraméter értékét.

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Előfeltételek 
- PHP-verziók 7.2.15 + és 7.3.2 +
- PHP PEAR 
- PHP – MySQL
- Azure Database for MariaDB kiszolgáló

1. Telepítse a [mysqlnd_azuret](https://github.com/microsoft/mysqlnd_azure) a [PECL](https://pecl.php.net/package/mysqlnd_azure)-mel. Azt javasoljuk, hogy a 1.1.0 + verziót használja.

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Keresse meg a kiterjesztés könyvtárát ( `extension_dir` ) a következő futtatásával:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Módosítsa a könyvtárakat a visszaadott mappába, és győződjön meg róla, hogy a `mysqlnd_azure.so` mappában található. 

4. Keresse meg az. ini fájlok mappáját az alábbi futtatásával: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Módosítsa a visszaadott mappába a címtárakat. 

6. Hozzon létre egy új. ini fájlt a következőhöz: `mysqlnd_azure` . Győződjön meg arról, hogy a név ABC-sorrendje a mysqnld után van-e, mivel a modulok az ini-fájlok neve szerint vannak betöltve. Ha például az `mysqlnd` . ini `10-mysqlnd.ini` nevet kapta, nevezze el a mysqlnd ini-t a következőként: `20-mysqlnd-azure.ini` .

7. Az új. ini fájlban adja hozzá a következő sorokat az átirányítás engedélyezéséhez.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>Előfeltételek 
- PHP-verziók 7.2.15 + és 7.3.2 +
- PHP – MySQL
- Azure Database for MariaDB kiszolgáló

1. A következő parancs futtatásával állapítsa meg, hogy a PHP x64-es vagy x86-os verzióját futtatja-e:

    ```cmd
    php -i | findstr "Thread"
    ```

2. Töltse le a [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) dll megfelelő x64-es vagy x86-os verzióját a [PECL](https://pecl.php.net/package/mysqlnd_azure) -ből, amely megfelel a PHP-verziójának. Azt javasoljuk, hogy a 1.1.0 + verziót használja.

3. Bontsa ki a zip-fájlt, és keresse meg a nevű DLL-t `php_mysqlnd_azure.dll` .

4. Keresse meg a kiterjesztés könyvtárát ( `extension_dir` ) az alábbi parancs futtatásával:

    ```cmd
    php -i | find "extension_dir"
    ```

5. Másolja a `php_mysqlnd_azure.dll` fájlt a 4. lépésben visszaadott könyvtárba. 

6. Keresse meg a fájlt tartalmazó PHP-mappát `php.ini` a következő parancs használatával:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Módosítsa a `php.ini` fájlt, és adja hozzá a következő extra sorokat az átirányítás engedélyezéséhez. 

    A dinamikus bővítmények szakaszban: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    A modul beállításai szakaszban:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>Átirányítás megerősítése

Azt is megerősítheti, hogy az átirányítás az alábbi minta PHP-kóddal van konfigurálva. Hozzon létre egy nevű PHP `mysqlConnect.php` -fájlt, és illessze be az alábbi kódot. Frissítse a kiszolgálónevet, a felhasználónevet és a jelszót a saját nevével. 
 
 ```php
<?php
$host = '<yourservername>.mariadb.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enableRedirect: ", ini_get("mysqlnd_azure.enableRedirect"), "\n";
  $db = mysqli_init();
  //The connection must be configured with SSL for redirection test
  $link = mysqli_real_connect ($db, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL);
  if (!$link) {
     die ('Connect error (' . mysqli_connect_errno() . '): ' . mysqli_connect_error() . "\n");
  }
  else {
    echo $db->host_info, "\n"; //if redirection succeeds, the host_info will differ from the hostname you used used to connect
    $res = $db->query('SHOW TABLES;'); //test query with the connection
    print_r ($res);
    $db->close();
  }
?>
 ```

## <a name="next-steps"></a>Következő lépések
További információ a kapcsolatok karakterláncáról: a [kapcsolatok karakterláncai](howto-connection-string.md).
