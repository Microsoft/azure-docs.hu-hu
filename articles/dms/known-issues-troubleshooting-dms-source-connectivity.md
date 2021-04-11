---
title: A forrás-adatbázisok összekapcsolásával kapcsolatos problémák
titleSuffix: Azure Database Migration Service
description: Ismerje meg, hogyan lehet elhárítani a Azure Database Migration Service a forrás-adatbázisokhoz való csatlakoztatásával kapcsolatos ismert problémákat/hibákat.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: edc420cb1e79ed6d99a55524764cb164bd2edaf5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641347"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>A forrásadatbázisokhoz való csatlakozás során fellépő DMS-hibák elhárítása

A következő cikk részletesen ismerteti, hogyan kezelheti a Azure Database Migration Service (DMS) és a forrásadatbázis összekapcsolásakor előforduló lehetséges problémákat. Az alábbi szakaszok egy adott forrás-adatbázisra vonatkoznak, amely felsorolja az esetlegesen felmerülő hibát, valamint a kapcsolat hibakeresésével kapcsolatos információkra mutató hivatkozásokat.

## <a name="sql-server"></a>SQL Server

A forrás-SQL Server adatbázishoz való csatlakozáshoz és a velük való kapcsolódáshoz kapcsolódó lehetséges problémák a következő táblázatban vannak megadva.

| Hiba         | Ok és hibaelhárítás részletei |
| ------------- | ------------- |
| Az SQL-kapcsolatok sikertelenek. Hálózattal kapcsolatos vagy példányspecifikus hiba történt az SQL Serverhez való kapcsolódás során. A kiszolgáló nem található vagy nem érhető el. Ellenőrizze, hogy a példány neve helyes-e, és hogy a SQL Server távoli kapcsolatok engedélyezésére van-e konfigurálva.<br> | Ez a hiba akkor fordul elő, ha a szolgáltatás nem találja a forráskiszolgáló helyét. A probléma megoldásához tekintse meg a [következő cikket: hiba történt a forrás SQL Serverhoz való kapcsolódáskor, ha dinamikus portot vagy nevesített példányt használ](./known-issues-troubleshooting-dms.md#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance). |
| **53** -es hiba – az SQL-kapcsolatok sikertelenek. (Hibakód: 1, 2, 5, 53, 233, 258, 1225, 11001)<br><br> | Ez a hiba akkor fordul elő, ha a szolgáltatás nem tud csatlakozni a forráskiszolgálóról. A probléma megoldásához tekintse meg a következő erőforrásokat, majd próbálkozzon újra. <br><br>  [Interaktív felhasználói útmutató a kapcsolódási probléma megoldásához](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [SQL Server áttelepítésének előfeltételei a Azure SQL Database](./pre-reqs.md#prerequisites-for-migrating-sql-server-to-azure-sql-managed-instance) <br><br> [SQL Server Azure SQL felügyelt példányra való áttelepítésének előfeltételei](./pre-reqs.md#prerequisites-for-migrating-sql-server-to-azure-sql-managed-instance) |
| **18456** -es hiba – a bejelentkezés sikertelen.<br> | Ez a hiba akkor fordul elő, ha a szolgáltatás nem tud kapcsolódni a forrás-adatbázishoz a megadott T-SQL hitelesítő adatok használatával. A probléma megoldásához ellenőrizze a megadott hitelesítő adatokat. Tekintse meg [MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error) vagy a táblázat alatti megjegyzésben felsorolt hibaelhárítási dokumentumokat is, majd próbálkozzon újra. |
| A (z) "" formázott AccountName érték van {0} megadva. A AccountName várt formátuma tartománynév \ Felhasználónév<br> | Ez a hiba akkor fordul elő, ha a felhasználó a Windows-hitelesítést választja, de a felhasználónevet érvénytelen formátumban adja meg. A probléma megoldásához adja meg a felhasználónevet a Windows-hitelesítés megfelelő formátumában, vagy válassza az **SQL-hitelesítés** lehetőséget. |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

A forrás AWS RDS MySQL-adatbázishoz való csatlakozással kapcsolatos lehetséges problémák, valamint az alábbi táblázatban található információk kezelése.

| Hiba         | Ok és hibaelhárítás részletei |
| ------------- | ------------- |
| **Hiba [2003]**[HY000] – a csatlakoztatás sikertelen. HIBA [HY000] [MySQL] [ODBC x. x (w) illesztőprogram] nem lehet csatlakozni a MySQL-kiszolgálóhoz a következőn: {Server} (10060) | Ez a hiba akkor fordul elő, ha a MySQL ODBC-illesztő nem tud csatlakozni a forráskiszolgálóról. A probléma megoldásához tekintse meg a táblázat alatti megjegyzésben felsorolt hibaelhárítási dokumentumokat, majd próbálkozzon újra.<br> |
| **Hiba [2005]**[HY000] – a csatlakoztatás sikertelen. HIBA [HY000] [MySQL] [ODBC x. x (w) illesztőprogram] ismeretlen MySQL-kiszolgáló gazdagépe: {Server} | Ez a hiba akkor fordul elő, ha a szolgáltatás nem találja a forrás gazdagépet az RDS-ben. A probléma oka az lehet, hogy a felsorolt forrás nem létezik, vagy probléma van az RDS-infrastruktúrával. A probléma megoldásához tekintse meg a táblázat alatti megjegyzésben felsorolt hibaelhárítási dokumentumokat, majd próbálkozzon újra.<br> |
| **Hiba [1045]**[HY000] – a csatlakoztatás sikertelen. HIBA [HY000] [MySQL] [ODBC x. x (w) illesztőprogram] hozzáférés megtagadva a (z) {User} @ {Server} felhasználóhoz (jelszó használata: igen) | Ez a hiba akkor fordul elő, ha a MySQL ODBC-illesztő érvénytelen hitelesítő adatok miatt nem tud csatlakozni a forráskiszolgálóról. Ellenőrizze a megadott hitelesítő adatokat. Ha a probléma továbbra is fennáll, ellenőrizze, hogy a forrásoldali számítógép rendelkezik-e a megfelelő hitelesítő adatokkal. Előfordulhat, hogy alaphelyzetbe kell állítania a jelszót a konzolon. Ha a probléma továbbra is fennáll, tekintse meg a táblázat alatti megjegyzésben felsorolt hibaelhárítási dokumentumokat, majd próbálkozzon újra.<br> |
| **Hiba [9002]**[HY000] – a csatlakoztatás sikertelen. HIBA [HY000] [MySQL] [ODBC x. x (w) illesztőprogram] lehetséges, hogy a kapcsolatok karakterlánca nem megfelelő. Hivatkozásokat a portálon találhat.| Ez a hiba akkor fordul elő, ha a kapcsolódás sikertelen a kapcsolódási karakterlánccal kapcsolatos probléma miatt. Ellenőrizze, hogy érvényes-e a megadott kapcsolatok karakterlánca. A probléma megoldásához tekintse meg a táblázat alatti megjegyzésben felsorolt hibaelhárítási dokumentumokat, majd próbálkozzon újra.<br> |
| **Hiba történt a bináris naplózásban. A (z) binlog_format változó értéke {Value}. Módosítsa a sort.** | Ez a hiba akkor fordul elő, ha hiba történt a bináris naplózásban; a binlog_format változó értéke helytelen. A probléma megoldásához módosítsa a paraméter-csoport binlog_format a "sor" értékre, majd indítsa újra a példányt. További információ: [bináris naplózási beállítások és változók](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html) vagy [AWS RDS MySQL Database naplófájlok dokumentációja](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html).<br> |

> [!NOTE]
> A forrás AWS RDS MySQL-adatbázishoz való csatlakozással kapcsolatos hibák elhárításával kapcsolatban tekintse meg a következő forrásokat:
> *    [Az Amazon RDS kapcsolódási problémáinak elhárítása](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> *    [Hogyan az Amazon RDS Database-példánnyal való kapcsolódással kapcsolatos problémák megoldásához?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>AWS RDS PostgreSQL

A forrás AWS RDS PostgreSQL-adatbázishoz való csatlakozással kapcsolatos lehetséges problémák, valamint az alábbi táblázatban található információk kezelése.

| Hiba         | Ok és hibaelhárítás részletei |
| ------------- | ------------- |
| **Hiba [101]**[08001] – a csatlakoztatás sikertelen. HIBA [08001] időkorlát lejárt. | Ez a hiba akkor fordul elő, ha a postgres-illesztő nem tud csatlakozni a forráskiszolgálóról. A probléma megoldásához tekintse meg a táblázat alatti megjegyzésben felsorolt hibaelhárítási dokumentumokat, majd próbálkozzon újra. |
| **Hiba: a (z) wal_level paraméter értéke {Value}. A replikáció engedélyezéséhez módosítsa a "logikai" értékre.** | Ez a hiba akkor fordul elő, ha a wal_level paraméter értéke helytelen. A probléma megoldásához módosítsa a paraméter-csoport rds.logical_replication értékét 1-re, majd indítsa újra a példányt. További információkért lásd: [Előfeltételek az Azure PostgreSQL-re való áttelepítéshez a DMS](./tutorial-postgresql-azure-postgresql-online.md#prerequisites) vagy [a PostgreSQL használatával az Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html)-ben. |

> [!NOTE]
> A forrás AWS RDS PostgreSQL-adatbázishoz való csatlakozással kapcsolatos hibák elhárításával kapcsolatban tekintse meg a következő forrásokat:
> *    [Az Amazon RDS kapcsolódási problémáinak elhárítása](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> *    [Hogyan az Amazon RDS Database-példánnyal való kapcsolódással kapcsolatos problémák megoldásához?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>AWS RDS SQL Server

A forráshoz való csatlakozáshoz kapcsolódó lehetséges problémák a következő táblázatban találhatók: AWS RDS SQL Server Database, és hogyan kell kezelni őket.

| Hiba         | Ok és hibaelhárítás részletei |
| ------------- | ------------- |
| **53** -es hiba – az SQL-kapcsolatok sikertelenek. Hálózattal kapcsolatos vagy példányspecifikus hiba történt az SQL Serverhez való kapcsolódás során. A kiszolgáló nem található vagy nem volt elérhető. Ellenőrizze, hogy a példány neve helyes-e, és hogy a SQL Server távoli kapcsolatok engedélyezésére van-e konfigurálva. (szolgáltató: Nevesített csövek szolgáltatója, hiba: 40 – nem sikerült megnyitni a kapcsolódást SQL Server | Ez a hiba akkor fordul elő, ha a szolgáltatás nem tud csatlakozni a forráskiszolgálóról. A probléma megoldásához tekintse meg a táblázat alatti megjegyzésben felsorolt hibaelhárítási dokumentumokat, majd próbálkozzon újra. |
| **18456** -es hiba – a bejelentkezés sikertelen. A (z) {User} felhasználó bejelentkezése sikertelen | Ez a hiba akkor fordul elő, ha a szolgáltatás nem tud kapcsolódni a forrás-adatbázishoz a megadott T-SQL hitelesítő adatokkal. A probléma megoldásához ellenőrizze a megadott hitelesítő adatokat. Tekintse meg [MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error) vagy a táblázat alatti megjegyzésben felsorolt hibaelhárítási dokumentumokat is, és próbálkozzon újra. |
| **Hiba 87** – a kapcsolatok karakterlánca érvénytelen. Hálózattal kapcsolatos vagy példányspecifikus hiba történt az SQL Serverhez való kapcsolódás során. A kiszolgáló nem található vagy nem érhető el. Ellenőrizze, hogy a példány neve helyes-e, és hogy a SQL Server távoli kapcsolatok engedélyezésére van-e konfigurálva. (szolgáltató: SQL hálózati adapterek, hiba: 25 – a kapcsolati sztring érvénytelen) | Ez a hiba akkor fordul elő, ha a szolgáltatás nem tud csatlakozni a forráskiszolgálóról egy érvénytelen kapcsolati karakterlánc miatt. A probléma megoldásához ellenőrizze a megadott kapcsolódási karakterláncot. Ha a probléma továbbra is fennáll, tekintse meg a táblázat alatti megjegyzésben felsorolt hibaelhárítási dokumentumokat, majd próbálkozzon újra. |
| **Hiba – a kiszolgáló tanúsítványa nem megbízható.** Sikeresen létrejött egy csatlakozás a-kiszolgálóval, de hiba történt a bejelentkezési folyamat során. (szolgáltató: SSL-szolgáltató, hiba: 0 – a tanúsítványláncot olyan szolgáltató adta ki, amely nem megbízható.) | Ez a hiba akkor fordul elő, ha a használt tanúsítvány nem megbízható. A probléma megoldásához meg kell keresnie egy megbízható tanúsítványt, majd engedélyeznie kell azt a kiszolgálón. Azt is megteheti, hogy a kapcsolódáskor a megbízhatósági tanúsítvány lehetőséget választja. Ezt a műveletet csak akkor végezze el, ha már ismeri a használt tanúsítványt, és megbízik benne. <br> Az önaláírt tanúsítvánnyal titkosított TLS-kapcsolatok nem biztosítanak erős biztonságot – ezek az ember általi támadásra fogékonyak. Ne használja a TLS-t az önaláírt tanúsítványok használatával éles környezetben vagy az internethez csatlakozó kiszolgálókon. <br> További információ: az [SSL használata Microsoft SQL Server db-példánnyal](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html) vagy [oktatóanyaggal: RDS SQL Server migrálása az Azure-ba a DMS használatával](./index.yml). |
| **300** -es hiba – a felhasználó nem rendelkezik a szükséges engedélyekkel. A kiszolgáló ÁLLAPOTának megtekintése engedély megtagadva a (z) {Server} objektumon, adatbázis: {Database} | Ez a hiba akkor fordul elő, ha a felhasználó nem rendelkezik az áttelepítés elvégzéséhez szükséges engedéllyel. A probléma megoldásához tekintse meg a következő témakört: [kiszolgáló engedélyeinek megadása – Transact-SQL](/sql/t-sql/statements/grant-server-permissions-transact-sql) vagy [oktatóanyag: RDS SQL Server migrálása az Azure-ba a DMS használatával](./index.yml) további részletekért. |

> [!NOTE]
> Ha további információt szeretne a forrás AWS RDS SQL Serverhoz való csatlakozással kapcsolatos hibák elhárításával kapcsolatban, tekintse meg a következő forrásokat:
>
> *    [Az SQL Server kapcsolódási hibáinak megoldása](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)
> * [Hogyan az Amazon RDS Database-példánnyal való kapcsolódással kapcsolatos problémák megoldásához?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>Ismert problémák

* [Ismert problémák/áttelepítési korlátozások az online áttelepítéssel Azure SQL Database](./index.yml)
* [Ismert problémák/áttelepítési korlátozások az online áttelepítéssel Azure Database for MySQL](./known-issues-azure-mysql-online.md)
* [Ismert problémák/áttelepítési korlátozások az online áttelepítéssel Azure Database for PostgreSQL](./known-issues-azure-postgresql-online.md)

## <a name="next-steps"></a>Következő lépések

* Tekintse meg a [Azure Database Migration Service PowerShellt](/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0&preserve-view=true#data_migration)ismertető cikket.
* A Azure Portal használatával megtekintheti a [kiszolgáló paramétereinek konfigurálása Azure Database for MySQLben](../mysql/howto-server-parameters.md)című cikket.
* Tekintse meg a [Azure Database Migration Service használatának előfeltételeit ismertető](./pre-reqs.md)cikket.
* Tekintse meg a [Azure Database Migration Service használatának gyakori kérdéseit](./faq.md).
