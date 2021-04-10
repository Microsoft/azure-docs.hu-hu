---
title: Kapcsolat és lekérdezés – egyetlen kiszolgáló PostgreSQL
description: Hivatkozások a Azure Database for PostgreSQL egyetlen kiszolgálóhoz való csatlakozáshoz és a lekérdezések futtatásához.
services: postgresql
ms.service: postgresql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 09/21/2020
ms.openlocfilehash: 1506ce311fe443247050a36e1b9fa4600360ac6e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604141"
---
# <a name="connect-and-query-overview-for-azure-database-for-postgresql--single-server"></a>Az Azure Database for PostgreSQL egykiszolgálós üzemének csatlakozási és lekérdezési áttekintése

Az alábbi dokumentum olyan példákra mutató hivatkozásokat tartalmaz, amelyek bemutatják, hogyan csatlakozhat és végezhet lekérdezéseket Azure Database for PostgreSQL egyetlen kiszolgálóval. Ez az útmutató a TLS-ajánlásokat és-bővítményeket is tartalmazza, amelyek segítségével az alábbi támogatott nyelveken csatlakozhat a kiszolgálóhoz.

## <a name="quickstarts"></a>Rövid útmutatók

| Gyorsútmutató | Leírás |
|---|---|
|[PgAdmin](https://www.pgadmin.org/)|A pgAdmin használatával csatlakozhat a kiszolgálóhoz, és leegyszerűsíti az adatbázis-objektumok létrehozását, karbantartását és használatát.|
|[psql Azure Cloud Shell](quickstart-create-server-database-azure-cli.md#connect-to-the-azure-database-for-postgresql-server-by-using-psql)|Ez a cikk bemutatja, hogyan futtathat [**psql**](https://www.postgresql.org/docs/current/static/app-psql.html) -t a [Azure Cloud Shell](../cloud-shell/overview.md) a kiszolgálóhoz való kapcsolódáshoz, majd az utasítások futtatásához az adatbázisban lévő adatokat lekérdezni, beszúrni, frissíteni és törölni. A **psql** futtatható, ha telepítve van a fejlesztési környezetében|
|[PostgreSQL és VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)|A VS Code-hoz készült Azure Databases bővítmény (előzetes verzió) lehetővé teszi a PostgreSQL-kiszolgáló helyi és Felhőbeli tallózását és lekérdezését a gazdag IntelliSense-mel rendelkező scrapbooks használatával. |
|[PHP](connect-php.md)|Ez a rövid útmutató azt ismerteti, hogyan használható a PHP egy olyan program létrehozásához, amely egy adatbázishoz csatlakozik, és használja az adatbázis-objektumokkal végzett munkát az adatlekérdezéshez.|
|[Java](connect-java.md)|Ez a rövid útmutató azt ismerteti, hogyan használható a Java egy adatbázishoz való kapcsolódáshoz, és hogyan lehet az adatbázis-objektumokkal dolgozni az adatlekérdezéshez.|
|[Node.js](connect-nodejs.md)|Ez a rövid útmutató azt ismerteti, hogyan használható a Node.js egy olyan program létrehozásához, amely egy adatbázishoz csatlakozik, és hogyan lehet adatbázis-objektumokkal dolgozni az adatlekérdezéshez.|
|[.NET (C#)](connect-csharp.md)|Ez a rövid útmutató azt ismerteti, hogyan use.NET (C#) egy C#-program létrehozásához az adatbázishoz való kapcsolódáshoz és az adatbázis-objektumok használatával végzett adatlekérdezéshez.|
|[Ugrás](connect-go.md)|Ez a rövid útmutató bemutatja, hogyan használható a go egy adatbázishoz való kapcsolódáshoz. Emellett az adatok lekérdezéséhez és módosításához használatos Transact-SQL-utasításokat is bemutatja.|
|[Python](connect-python.md)|Ez a rövid útmutató azt ismerteti, hogyan használható a Python egy adatbázishoz való kapcsolódáshoz, és hogyan lehet az adatbázis-objektumokkal dolgozni az adatlekérdezéshez. |
|[Ruby](connect-ruby.md)|Ez a rövid útmutató azt ismerteti, hogyan használható a Ruby egy olyan program létrehozásához, amely egy adatbázishoz csatlakozik, és használja az adatbázis-objektumokkal végzett munkát az adatlekérdezéshez.|

## <a name="tls-considerations-for-database-connectivity"></a>TLS-megfontolások az adatbázis-kapcsolathoz

A Transport Layer Security (TLS) a Microsoft által szolgáltatott és a Azure Database for PostgreSQL-adatbázisokhoz való csatlakozást támogató összes illesztőprogram által használható. Nincs szükség speciális konfigurációra, de a TLS 1,2-et az újonnan létrehozott kiszolgálókhoz kell kikényszeríteni. Javasoljuk, hogy a TLS 1,0-es és 1,1-es verzióját használja, majd frissítse a TLS-verziót a kiszolgálókon. Lásd: [a TLS konfigurálása](howto-tls-configurations.md)

## <a name="postgresql-extensions"></a>PostgreSQL-bővítmények

A PostgreSQL lehetőséget nyújt az adatbázis funkcióinak kiterjesztésére a bővítmények használatával. A bővítmények több kapcsolódó SQL-objektumot kötegelnek egyetlen csomaggá, amely egyetlen paranccsal betölthető vagy eltávolítható az adatbázisból. Az adatbázisba való betöltés után a bővítmények ugyanúgy működnek, mint a beépített funkciók.

- [Postgres 11 bővítmény](./concepts-extensions.md#postgres-11-extensions)
- [Postgres 10 bővítmény](./concepts-extensions.md#postgres-10-extensions)
- [Postgres 9,6-bővítmények](./concepts-extensions.md#postgres-96-extensions)

További részletek: a [PostgreSQL-bővítmények használata egyetlen kiszolgálón](concepts-extensions.md).

## <a name="next-steps"></a>Következő lépések

- [Az adatáttelepítés a memóriakép és a visszaállítás használatával](howto-migrate-using-dump-and-restore.md)
- [Az adatáttelepítés importálással és exportálással](howto-migrate-using-export-and-import.md)
