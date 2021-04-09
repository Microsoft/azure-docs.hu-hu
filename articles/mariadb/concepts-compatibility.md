---
title: Illesztőprogramok és eszközök kompatibilitása – Azure Database for MariaDB
description: Ez a cikk a Azure Database for MariaDB rendszerrel kompatibilis MariaDB-illesztőprogramokat és-felügyeleti eszközöket ismerteti.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 49fd2c8da3ba0a44f4901bc6c7a2241e9e20df4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98659869"
---
# <a name="mariadb-drivers-and-management-tools-compatible-with-azure-database-for-mariadb"></a>A Azure Database for MariaDB-kompatibilis MariaDB-illesztőprogramok és-felügyeleti eszközök

Ez a cikk a Azure Database for MariaDB kompatibilis illesztőprogramokat és felügyeleti eszközöket ismerteti.

## <a name="mariadb-drivers"></a>MariaDB-illesztőprogramok

Azure Database for MariaDB a MariaDB-kiszolgáló közösségi kiadását használja. Ezért kompatibilis a programozási nyelvek és illesztőprogramok széles választékával. A MariaDB API-je és protokollja kompatibilis a MySQL által használt szolgáltatásokkal. Ez azt jelenti, hogy a MySQL-vel dolgozó összekötőknek a MariaDB-mel is együtt kell működniük.

A cél az, hogy támogassa a MariaDB-illesztőprogramok három legújabb verzióját, és a nyílt forráskódú Közösségből származó szerzők erőfeszítéseit folyamatosan javítsuk a MariaDB-illesztőprogramok működésének és használhatóságának folytatásán. A következő táblázat tartalmazza azon illesztőprogramok listáját, amelyeket teszteltek, és amelyek kompatibilisek a Azure Database for MariaDB 10,2-vel:

**Illesztő** | **Hivatkozások** | **Kompatibilis verziók** | **Nem kompatibilis verziók** | **Megjegyzések**
---|---|---|---|---
PHP | https://secure.php.net/downloads.php | 5,5, 5,6, 7. x | 5.3 | Az SSL-MySQLi rendelkező PHP 7,0-kapcsolathoz adja hozzá a MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERTt a kapcsolati karakterláncban. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> OEM set: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` beállítás hamis értékre.
.NET | [MySqlConnector a GitHubon](https://github.com/mysql-net/MySqlConnector) <br> [Telepítőcsomag a Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0,27 és utána | 0.26.5 és előtte |
MySQL-összekötő/háló | [MySQL-összekötő/háló](https://github.com/mysql/mysql-connector-net) | 8,0, 7,0, 6,10 |  | A kódolási hibák miatt a kapcsolatok sikertelenek lehetnek bizonyos nem UTF8 Windows rendszereken.
Node.js |  [MySQLjs a GitHubon](https://github.com/mysqljs/mysql/) <br> Telepítőcsomag a NPM-ből:<br> Futtatás `npm install mysql` a NPM | 2.15 | 2.14.1 és előtte
GO | https://github.com/go-sql-driver/mysql/releases | 1,3, 1,4 | 1,2 és előtte | Használja az `allowNativePasswords=true` 1,3-es verzióhoz tartozó kapcsolatok karakterláncát. Az 1,4-es verzió egy javítást tartalmaz, és már `allowNativePasswords=true` nem szükséges.
Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2,0, 2,1, 2,2 | 1.2.2 és korábban |
Java | https://downloads.mariadb.org/connector-java/ | 2,1, 2,0, 1,6 | 1.5.5 és előtte |

## <a name="management-tools"></a>Kezelőeszközök

A kompatibilitási előny az adatbázis-felügyeleti eszközökre is kiterjed. A meglévő eszközeinek továbbra is működniük kell Azure Database for MariaDBsal, feltéve, hogy az adatbázis-manipuláció a felhasználói engedélyek határain belül működik. Az alábbi táblázatban három olyan általános adatbázis-felügyeleti eszköz található, amelyet teszteltek, és amelyek kompatibilisek a Azure Database for MariaDB 10,2-vel:

| Művelet | **MySQL Workbench 6. x és fel** | **Navicat 12** | **PHPMyAdmin 4. x és fel**
---|---|---|---
Létrehozás, frissítés, olvasás, írás, törlés | X | X | X
SSL-kapcsolat | X | X | X
SQL-lekérdezés automatikus befejezése | X | X |
Az adatimportálás és-exportálás | X | X | X
Exportálás több formátumba | X | X | X
Biztonsági mentés és visszaállítás |  | X |
Kiszolgáló paramétereinek megjelenítése | X | X | X
Ügyfélkapcsolatok megjelenítése | X | X | X

## <a name="next-steps"></a>Következő lépések

- [Az Azure Database for MariaDB-hez való csatlakozás hibáinak elhárítása](howto-troubleshoot-common-connection-issues.md)