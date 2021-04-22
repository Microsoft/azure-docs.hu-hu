---
title: Kapcsolattárak – Azure Database for PostgreSQL – Egyetlen kiszolgáló
description: Ez a cikk számos kódtárat és illesztőprogramot ismertet, amelyek használhatók az alkalmazásoknak az egyetlen kiszolgálóhoz való Azure Database for PostgreSQL lekérdezéséhez.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: b8a1526605195b5eb24d8044f42b70ca5336bf7c
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878307"
---
# <a name="connection-libraries-for-azure-database-for-postgresql---single-server"></a>Kapcsolattárak Azure Database for PostgreSQL – Egyetlen kiszolgáló
Ez a cikk azokat a kódtárakat és illesztőprogramokat sorolja fel, amelyek használatával a fejlesztők alkalmazásokat fejleszthet a csatlakozáshoz és a lekérdezési Azure Database for PostgreSQL.

## <a name="client-interfaces"></a>Ügyféloldali felületek
A PostgreSQL-kiszolgálóhoz való csatlakozáshoz használt legtöbb nyelvi ügyfélkódtár külső projekt, amelyek függetlenül érhetők el. A felsorolt kódtárak a Windows, Linux és Mac platformokon támogatottak a virtuális gépekhez való Azure Database for PostgreSQL. A További lépések szakaszban több rövid útmutató is szerepel.

| **Nyelv** | **Ügyféloldali felület** | **További információ** | **Letöltés** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | DB API 2.0-kompatibilis | [Letöltés](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | Adatbázis-bővítmény | [Telepítse](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pg npm-csomag](https://www.npmjs.com/package/pg) | Tiszta JavaScript nem blokkoló ügyfél | [Telepítse](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | 4. típusú JDBC-illesztő | [Letöltés](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Pg gem](https://deveiate.org/code/pg/) | Ruby-felület | [Letöltés](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Csomag pq](https://godoc.org/github.com/lib/pq) | Pure Go postgres-illesztő | [Telepítse](https://github.com/lib/pq/blob/master/README.md) |
| C \# / .NET | [Npgsql](https://www.npgsql.org/) | ADO.NET adatszolgáltató | [Letöltés](https://dotnet.microsoft.com/download) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | ODBC-illesztő | [Letöltés](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Elsődleges C nyelvi felület | Tartalmazza |
| C++ | [libpqxx](http://pqxx.org/) | Új stílusú C++ felület | [Letöltés](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Következő lépések
Olvassa el az alábbi rövid útmutatót, amely bemutatja, hogyan csatlakozhat a Azure Database for PostgreSQL és hogyan kérdezheti le az adatokat a választott nyelven:

[Python](./connect-python.md)  |  [Node.JS](./connect-nodejs.md)  |  [Java](./connect-java.md)  |  [Ruby](./connect-ruby.md)  |  [PHP](./connect-php.md)  |  [.NET (C#)](./connect-csharp.md)  |  [Go (Ugrás)](./connect-go.md)
