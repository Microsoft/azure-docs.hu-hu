---
title: Illesztőprogramok és eszközök kompatibilitása – Azure Database for MySQL
description: Ez a cikk a Azure Database for MySQL rendszerrel kompatibilis MySQL-illesztőprogramokat és-felügyeleti eszközöket ismerteti.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c2faf8e8b6a814989fa9e1693fddeedb525df13a
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "100006853"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>A Azure Database for MySQL-kompatibilis MySQL-illesztőprogramok és-felügyeleti eszközök
Ez a cikk a Azure Database for MySQL kompatibilis illesztőprogramokat és felügyeleti eszközöket ismerteti.

## <a name="mysql-drivers"></a>MySQL-illesztőprogramok
Azure Database for MySQL a MySQL-adatbázis világ legnépszerűbb közösségi kiadását használja. Ezért kompatibilis a programozási nyelvek és illesztőprogramok széles választékával. A cél az, hogy támogassa a legújabb MySQL-illesztőprogramokat, és a nyílt forráskódú Közösségből származó szerzőkkel folytatott erőfeszítésekkel folyamatosan javítsa a MySQL-illesztőprogramok funkcióit és használhatóságát. A tesztelt és a Azure Database for MySQL 5,6-es és 5,7-es verzióval kompatibilis illesztőprogramok listáját az alábbi táblázat tartalmazza:

| **Programozási nyelv** | **Illesztő** | **Hivatkozások** | **Kompatibilis verziók** | **Nem kompatibilis verziók** | **Megjegyzések** |
| :----------------------- | :--------- | :-------- | :---------------------- | :------------------------ | :-------- |
| PHP | mysqli, pdo_mysql, mysqlnd | https://secure.php.net/downloads.php | 5,5, 5,6, 7. x | 5.3 | Az SSL-MySQLi rendelkező PHP 7,0-kapcsolathoz adja hozzá a MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERTt a kapcsolati karakterláncban. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> OEM set: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` beállítás hamis értékre.|
| .NET | Aszinkron MySQL-összekötő a .NET-hez | https://github.com/mysql-net/MySqlConnector <br> [Telepítőcsomag a Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0,27 és utána | 0.26.5 és előtte | |
| .NET | MySQL-összekötő/háló | https://github.com/mysql/mysql-connector-net | 6.6.3, 7,0, 8,0 |  | A kódolási hibák miatt a kapcsolatok sikertelenek lehetnek bizonyos nem UTF8 Windows rendszereken. |
| Node.js | mysqljs | https://github.com/mysqljs/mysql/ <br> Telepítőcsomag a NPM-ből:<br> Futtatás `npm install mysql` a NPM | 2.15 | 2.14.1 és előtte | |
| Node.js | csomópont – mysql2 | https://github.com/sidorares/node-mysql2 | 1.3.4 + | | |
| Go | Go MySQL-illesztőprogram | https://github.com/go-sql-driver/mysql/releases | 1,3, 1,4 | 1,2 és előtte | Használja az `allowNativePasswords=true` 1,3-es verzióhoz tartozó kapcsolatok karakterláncát. Az 1,4-es verzió egy javítást tartalmaz, és már `allowNativePasswords=true` nem szükséges. |
| Python | MySQL-összekötő/Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2,0, 2,1, 2,2, a 8.0.16 + és a MySQL 8,0 használata  | 1.2.2 és korábban | |
| Python | PyMySQL | https://pypi.org/project/PyMySQL/ | 0.7.11, 0.8.0, 0.8.1, 0.9.3 + | 0.9.0-0.9.2 (regresszió a web2py-ben) | |
| Java | MariaDB-összekötő/J | https://downloads.mariadb.org/connector-java/ | 2,1, 2,0, 1,6 | 1.5.5 és előtte | | 
| Java | MySQL-összekötő/J | https://github.com/mysql/mysql-connector-j | 5.1.21 +, használja a 8.0.17 és a MySQL 8,0 | 5.1.20 és alacsonyabb | |
| C | MySQL-összekötő/C (libmysqlclient) | https://dev.mysql.com/doc/refman/5.7/en/c-api-implementations.html | 6.0.2 + | | |
| C | MySQL-összekötő/ODBC (MyODBC) | https://github.com/mysql/mysql-connector-odbc | 3.51.29 + | | |
| C++ | MySQL-összekötő/C++ | https://github.com/mysql/mysql-connector-cpp | 1.1.9 + | 1.1.3-es és alacsonyabb | | 
| C++ | MySQL + +| https://github.com/tangentsoft/mysqlpp | 3.2.3 + | | |
| Ruby | mysql2 | https://github.com/brianmario/mysql2 | 0.4.10 + | | |
| R | RMySQL | https://github.com/rstats-db/RMySQL | 0.10.16 + | | |
| Swift | MySQL – Swift | https://github.com/novi/mysql-swift | 0.7.2 + | | |
| Swift | gőz/MySQL | https://github.com/vapor/mysql-kit | 2.0.1 + | | |

## <a name="management-tools"></a>Kezelőeszközök
A kompatibilitási előny az adatbázis-felügyeleti eszközökre is kiterjed. A meglévő eszközeinek továbbra is működniük kell Azure Database for MySQLsal, feltéve, hogy az adatbázis-manipuláció a felhasználói engedélyek határain belül működik. A következő táblázatban található három általános adatbázis-felügyeleti eszköz, amelyet teszteltek, és amelyek kompatibilisek a Azure Database for MySQL 5,6-as és 5,7-es verzióval:

|                                     | **MySQL Workbench 6. x és fel** | **Navicat 12** | **PHPMyAdmin 4. x és fel** | **dbForge Studio for MySQL 9,0** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------| :------------------------------- |
| **Létrehozás, frissítés, olvasás, írás, törlés** | X | X | X | X |
| **SSL-kapcsolat** | X | X | X | X |
| **SQL-lekérdezés automatikus befejezése** | X | X |  | X |
| **Az adatimportálás és-exportálás** | X | X | X | X |
| **Exportálás több formátumba** | X | X | X | X |
| **Biztonsági mentés és visszaállítás** |  | X |  | X |
| **Kiszolgáló paramétereinek megjelenítése** | X | X | X | X |
| **Ügyfélkapcsolatok megjelenítése** | X | X | X | X |

## <a name="next-steps"></a>Következő lépések

- [Az Azure Database for MySQL csatlakoztatási hibáinak elhárítása](howto-troubleshoot-common-connection-issues.md)
