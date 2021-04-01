---
title: Kapcsolat és lekérdezés – rugalmas kiszolgáló PostgreSQL
description: Hivatkozások a Azure Database for PostgreSQL rugalmas kiszolgálóhoz való csatlakozást és a lekérdezések futtatását bemutató rövid útmutatókra.
services: postgresql
ms.service: postgresql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 12/08/2020
ms.openlocfilehash: ee3b1f7db8bdafb1233b32579e032e8c864c37a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97364579"
---
# <a name="connect-and-query-overview-for-azure-database-for-postgresql--flexible-server"></a>A PostgreSQL-hez készült Azure Database-hez készült kapcsolat és lekérdezés áttekintése – rugalmas kiszolgáló

Az alábbi dokumentum olyan példákra mutató hivatkozásokat tartalmaz, amelyek bemutatják, hogyan csatlakozhat és végezhet lekérdezéseket Azure Database for PostgreSQL egyetlen kiszolgálóval. Ez az útmutató a TLS-ajánlásokat és-bővítményeket is tartalmazza, amelyek segítségével az alábbi támogatott nyelveken csatlakozhat a kiszolgálóhoz.

>[!IMPORTANT]
> Azure Database for PostgreSQL rugalmas kiszolgáló **előzetes** verzióban érhető el.

## <a name="quickstarts"></a>Rövid útmutatók

| Gyorsútmutató | Leírás |
|---|---|
|[PgAdmin](https://www.pgadmin.org/)|A pgAdmin használatával csatlakozhat a kiszolgálóhoz, és leegyszerűsíti az adatbázis-objektumok létrehozását, karbantartását és használatát.|
|[psql Azure Cloud Shell](./quickstart-create-server-cli.md#connect-using-postgresql-command-line-client)|Ez a cikk bemutatja, hogyan futtathat [**psql**](https://www.postgresql.org/docs/current/static/app-psql.html) -t a [Azure Cloud Shell](../../cloud-shell/overview.md) a kiszolgálóhoz való kapcsolódáshoz, majd az utasítások futtatásához az adatbázisban lévő adatokat lekérdezni, beszúrni, frissíteni és törölni. A **psql** futtatható, ha telepítve van a fejlesztési környezetében|
|[Python](connect-python.md)|Ez a rövid útmutató azt ismerteti, hogyan használható a Python egy adatbázishoz való kapcsolódáshoz, és hogyan lehet az adatbázis-objektumokkal dolgozni az adatlekérdezéshez. |
|[Django App Service](tutorial-django-app-service-postgres.md)|Ez az oktatóanyag azt mutatja be, hogyan használható a Ruby egy olyan program létrehozásához, amely egy adatbázishoz csatlakozik, és hogyan használhatja az adatbázis-objektumokat az adatlekérdezéshez.|

## <a name="tls-considerations-for-database-connectivity"></a>TLS-megfontolások az adatbázis-kapcsolathoz

A Transport Layer Security (TLS) a Microsoft által szolgáltatott és a Azure Database for PostgreSQL-adatbázisokhoz való csatlakozást támogató összes illesztőprogram által használható. Nincs szükség speciális konfigurációra, de a TLS 1,2-et az újonnan létrehozott kiszolgálókhoz kell kikényszeríteni. Javasoljuk, hogy a TLS 1,0-es és 1,1-es verzióját használja, majd frissítse a TLS-verziót a kiszolgálókon. Lásd: [a TLS konfigurálása](how-to-connect-tls-ssl.md)

## <a name="postgresql-extensions"></a>PostgreSQL-bővítmények

A PostgreSQL lehetőséget nyújt az adatbázis funkcióinak kiterjesztésére a bővítmények használatával. A bővítmények több kapcsolódó SQL-objektumot kötegelnek egyetlen csomaggá, amely egyetlen paranccsal betölthető vagy eltávolítható az adatbázisból. Az adatbázisba való betöltés után a bővítmények ugyanúgy működnek, mint a beépített funkciók.

- [Postgres 12 bővítmény](./concepts-extensions.md#postgres-12-extensions)
- [Postgres 11 bővítmény](./concepts-extensions.md#postgres-11-extensions)
- [dblink és postgres_fdw](./concepts-extensions.md#dblink-and-postgres_fdw)
- [pg_prewarm](./concepts-extensions.md#pg_prewarm)
- [pg_stat_statements](./concepts-extensions.md#pg_stat_statements)

További részletek: a [PostgreSQL-bővítmények használata rugalmas kiszolgálón](concepts-extensions.md).

## <a name="next-steps"></a>Következő lépések

- [Az adatáttelepítés a memóriakép és a visszaállítás használatával](../howto-migrate-using-dump-and-restore.md)
- [Az adatáttelepítés importálással és exportálással](../howto-migrate-using-export-and-import.md)
