---
title: A T-SQL szolgáltatásbeli különbségek a szinapszis SQL-ben
description: A szinapszis SQL-ben elérhető Transact-SQL-funkciók listája.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: b79ad8212c3c31bd0759b576c640e61ccd7a1c0b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604549"
---
# <a name="transact-sql-features-supported-in-azure-synapse-sql"></a>Az Azure szinapszis SQL által támogatott Transact-SQL-funkciók

Az Azure szinapszis SQL egy big data analitikus szolgáltatás, amely lehetővé teszi, hogy a T-SQL nyelv használatával lekérdezéseket és elemzéseket lehessen készíteni az adatairól. Az adatelemzéshez a SQL Server és Azure SQL Database által használt SQL-nyelv szabványos ANSI-kompatibilis dialektusát használhatja. 

A Transact-SQL nyelv használata a kiszolgáló nélküli SQL-készletben és a dedikált modellben különböző objektumokra hivatkozhat, és a támogatott funkciók némelyike eltérő lehet. Ezen az oldalon magas szintű Transact-SQL nyelvi különbségeket talál a szinapszis SQL felhasználási modelljei között.

## <a name="database-objects"></a>Adatbázis-objektumok

A szinapszis SQL felhasználási modelljei lehetővé teszik különböző adatbázis-objektumok használatát. A támogatott objektumtípusok összehasonlítása az alábbi táblázatban látható:

|   | Dedikált | Kiszolgáló nélküli |
| --- | --- | --- |
| **Táblák** | [Igen](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) | Nem, a kiszolgáló nélküli modell csak az [Azure Storage](#storage-options) -ba helyezett külső adatforrásokat kérdezheti le |
| **Megjelenítések** | [Igen](/sql/t-sql/statements/create-view-transact-sql?view=azure-sqldw-latest&preserve-view=true). A nézetek a dedikált modellben elérhető [lekérdezési nyelvi elemeket](#query-language) is használhatják. | [Igen](/sql/t-sql/statements/create-view-transact-sql?view=azure-sqldw-latest&preserve-view=true). A nézetek a kiszolgáló nélküli modellben elérhető [lekérdezési nyelvi elemeket](#query-language) is használhatják. |
| **Sémák** | [Igen](/sql/t-sql/statements/create-schema-transact-sql?view=azure-sqldw-latest&preserve-view=true) | [Igen](/sql/t-sql/statements/create-schema-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| **ideiglenes táblákkal** | [Igen](../sql-data-warehouse/sql-data-warehouse-tables-temporary.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | No |
| **Eljárások** | [Igen](/sql/t-sql/statements/create-procedure-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Yes |
| **Functions** | [Igen](/sql/t-sql/statements/create-function-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) | Igen, csak a beágyazott táblázat értékű függvények. |
| **Eseményindítók** | Nem | Nem |
| **Külső táblák** | [Igen](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true). Lásd: támogatott [adatformátumok](#data-formats). | [Igen](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true). Lásd: támogatott [adatformátumok](#data-formats). |
| **Gyorsítótárazási lekérdezések** | Igen, több űrlap (SSD-alapú gyorsítótárazás, memóriabeli, eredményhalmazt gyorsítótárazás). Emellett a rendszer az anyagbeli nézetet is támogatja | No |
| **Ideiglenes változók** | [Nem](/sql/t-sql/data-types/table-transact-sql?view=azure-sqldw-latest&preserve-view=true), ideiglenes táblák használata | No |
| **[Tábla eloszlása](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**               | Igen | Nem |
| **[Tábla indexei](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                           | Igen | Nem |
| **[Tábla partíciói](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                     | Igen | Nem |
| **[Statisztika](develop-tables-statistics.md)**            | Igen | Yes |
| **[Számítási feladatok kezelése, erőforrás-osztályok és Egyidejűség-vezérlés](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | Igen    | Nem |
| **Cost Control** | Igen, vertikális Felskálázási és leskálázási műveletek használatával. | Igen, [a Azure Portal vagy a T-SQL eljárás](./data-processed.md#cost-control)használatával. |

## <a name="query-language"></a>Lekérdezés nyelve

A szinapszis SQL-ben használt lekérdezési nyelvek különböző támogatott funkciókat tartalmazhatnak a használati modelltől függően. Az alábbi táblázat a Transact-SQL-dialektusokban a legfontosabb lekérdezési nyelvi különbségeket ismerteti:

|   | Dedikált | Kiszolgáló nélküli |
| --- | --- | --- |
| **SELECT utasítás** | Igen. A Transact-SQL-lekérdezési záradékok nem támogatottak [az XML/JSON](/sql/t-sql/queries/select-for-clause-transact-sql?view=azure-sqldw-latest&preserve-view=true)-hoz, a [egyezéshez](/sql/t-sql/queries/match-sql-graph?view=azure-sqldw-latest&preserve-view=true), az ELTOLÁShoz/lekéréshez. | Igen. A Transact-SQL-lekérdezési záradékok nem támogatottak [az XML](/sql/t-sql/queries/select-for-clause-transact-sql?view=azure-sqldw-latest&preserve-view=true), a [Match](/sql/t-sql/queries/match-sql-graph?view=azure-sqldw-latest&preserve-view=true), a [prediktív](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true), a GROUPNG és a lekérdezési célzásokhoz. |
| **INSERT utasítás** | Igen | Nem |
| **FRISSÍTÉSI utasítás** | Igen | Nem |
| **Utasítás törlése** | Igen | Nem |
| **MERGE utasítás** | Igen ([előzetes](/sql/t-sql/statements/merge-transact-sql?view=azure-sqldw-latest&preserve-view=true)verzió) | No |
| **[Tranzakciók](develop-transactions.md)** | Yes | Igen, a meta-adatobjektumokra alkalmazható. |
| **[Címkék](develop-label.md)** | Igen | Nem |
| **Adatterhelés** | Igen. Az előnyben részesített segédprogram [másolási](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) utasítás, de a rendszer a tömeges betöltést (BCP) és a [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true) is támogatja az adatok betöltéséhez. | No |
| **Adatexportálás** | Igen. A [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true)használata. | Igen. A [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true)használata. |
| **Típusok** | Igen, az összes Transact-SQL típus, kivéve a [kurzort](/sql/t-sql/data-types/cursor-transact-sql?view=azure-sqldw-latest&preserve-view=true), a [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?view=azure-sqldw-latest&preserve-view=true), az [ntext, a Text és a képet](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?view=azure-sqldw-latest&preserve-view=true), a [ROWVERSION](/sql/t-sql/data-types/rowversion-transact-sql?view=azure-sqldw-latest&preserve-view=true), a [térbeli típusokat](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?view=azure-sqldw-latest&preserve-view=true), az [SQL- \_ változatot](/sql/t-sql/data-types/sql-variant-transact-sql?view=azure-sqldw-latest&preserve-view=true)és az [XML](/sql/t-sql/xml/xml-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Igen, az összes Transact-SQL-típus a [kurzor](/sql/t-sql/data-types/cursor-transact-sql?view=azure-sqldw-latest&preserve-view=true), a [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?view=azure-sqldw-latest&preserve-view=true), az [ntext, a Text és a képek](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?view=azure-sqldw-latest&preserve-view=true), a [ROWVERSION](/sql/t-sql/data-types/rowversion-transact-sql?view=azure-sqldw-latest&preserve-view=true), a [térbeli típusok](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?view=azure-sqldw-latest&preserve-view=true), az [SQL \_ Variant](/sql/t-sql/data-types/sql-variant-transact-sql?view=azure-sqldw-latest&preserve-view=true), az [XML](/sql/t-sql/xml/xml-transact-sql?view=azure-sqldw-latest&preserve-view=true)és a Table típus kivételével |
| **Adatbázisközi lekérdezések** | No | Igen, beleértve a [use](/sql/t-sql/language-elements/use-transact-sql?view=azure-sqldw-latest&preserve-view=true) utasítást is. |
| **Beépített függvények (elemzés)** | Igen, minden Transact-SQL [analitikai](/sql/t-sql/functions/analytic-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), átalakítási, [dátum és idő](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), logikai, [matematikai](/sql/t-sql/functions/mathematical-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true) függvény, kivéve a választás és az [elemzés](/sql/t-sql/functions/parse-transact-sql?view=azure-sqldw-latest&preserve-view=true) [lehetőséget](/sql/t-sql/functions/logical-functions-choose-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Igen, az összes Transact-SQL [analitikai](/sql/t-sql/functions/analytic-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), átalakítási, [dátum és idő](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), logikai és [matematikai](/sql/t-sql/functions/mathematical-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true) függvény. |
| **Beépített függvények (szöveg)** | Igen. Minden Transact-SQL [karakterlánc](/sql/t-sql/functions/string-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true)-, [JSON](/sql/t-sql/functions/json-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true)-és rendezési függvény, kivéve a [STRING_ESCAPE](/sql/t-sql/functions/string-escape-transact-sql?view=azure-sqldw-latest&preserve-view=true) és a [fordítást](/sql/t-sql/functions/translate-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Igen. Minden Transact-SQL [sztring](/sql/t-sql/functions/string-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true)-, [JSON](/sql/t-sql/functions/json-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true)-és rendezési funkció. |
| **Beépített Table-Value függvények** | Igen, a [Transact-SQL sorhalmaz függvények](/sql/t-sql/functions/functions?view=azure-sqldw-latest&preserve-view=true#rowset-functions), kivéve a [OpenXML](/sql/t-sql/functions/openxml-transact-sql?view=azure-sqldw-latest&preserve-view=true), a [index](/sql/t-sql/functions/opendatasource-transact-sql?view=azure-sqldw-latest&preserve-view=true), a [LekérdezésMegnyitása](/sql/t-sql/functions/openquery-transact-sql?view=azure-sqldw-latest&preserve-view=true)és a [OpenRowset](/sql/t-sql/functions/openrowset-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Igen, a [Transact-SQL sorhalmaz függvények](/sql/t-sql/functions/functions?view=azure-sqldw-latest&preserve-view=true#rowset-functions), kivéve a [OpenXML](/sql/t-sql/functions/openxml-transact-sql?view=azure-sqldw-latest&preserve-view=true), a [index](/sql/t-sql/functions/opendatasource-transact-sql?view=azure-sqldw-latest&preserve-view=true)és a [LekérdezésMegnyitása](/sql/t-sql/functions/openquery-transact-sql?view=azure-sqldw-latest&preserve-view=true)  |
| **Összesítések** |  Transact-SQL beépített összesítések, kivéve [CHECKSUM_AGG](/sql/t-sql/functions/checksum-agg-transact-sql?view=azure-sqldw-latest&preserve-view=true) és [GROUPING_ID](/sql/t-sql/functions/grouping-id-transact-sql?view=azure-sqldw-latest&preserve-view=true) | A Transact-SQL beépített összesítései. |
| **Operátorok** | Igen, az összes [Transact-SQL operátor](/sql/t-sql/language-elements/operators-transact-sql?view=azure-sqldw-latest&preserve-view=true) , kivéve a [! >](/sql/t-sql/language-elements/not-greater-than-transact-sql?view=azure-sqldw-latest&preserve-view=true) és [! <](/sql/t-sql/language-elements/not-less-than-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Igen, az összes [Transact-SQL operátor](/sql/t-sql/language-elements/operators-transact-sql?view=azure-sqldw-latest&preserve-view=true)  |
| **A folyamat vezérlése** | Igen. Minden [Transact-SQL-vezérlési utasítás a](/sql/t-sql/language-elements/control-of-flow?view=azure-sqldw-latest&preserve-view=true) [Folytatás](/sql/t-sql/language-elements/continue-transact-sql?view=azure-sqldw-latest&preserve-view=true), a [goto](/sql/t-sql/language-elements/goto-transact-sql?view=azure-sqldw-latest&preserve-view=true), a [Return](/sql/t-sql/language-elements/return-transact-sql?view=azure-sqldw-latest&preserve-view=true), a [use](/sql/t-sql/language-elements/use-transact-sql?view=azure-sqldw-latest&preserve-view=true)és a [waitfor](/sql/t-sql/language-elements/waitfor-transact-sql?view=azure-sqldw-latest&preserve-view=true) kivételével | Igen. Az összes [Transact-SQL Control-flow utasítás](/sql/t-sql/language-elements/control-of-flow?view=azure-sqldw-latest&preserve-view=true) kiválaszthatja a lekérdezést a `WHILE (...)` feltételben |
| **DDL-utasítások (létrehozás, módosítás, eldobás)** | Igen. Minden olyan Transact-SQL DDL-utasítás, amely a támogatott objektumtípusok esetében érvényes | Igen. Minden olyan Transact-SQL DDL-utasítás, amely a támogatott objektumtípusok esetében érvényes |

## <a name="security"></a>Biztonság

A szinapszis SQL lehetővé teszi, hogy az adatai védelméhez és a hozzáférés szabályozásához beépített biztonsági funkciókat használjon. Az alábbi táblázat összehasonlítja a szinapszis SQL-felhasználási modellek közötti magas szintű különbségeket.

|   | Dedikált | Kiszolgáló nélküli |
| --- | --- | --- |
| **Bejelentkezések** | N/A (csak a benne foglalt felhasználók támogatottak az adatbázisokban) | Yes |
| **Felhasználók** |  N/A (csak a benne foglalt felhasználók támogatottak az adatbázisokban) | Yes |
| **[Tartalmazott felhasználók](/sql/relational-databases/security/contained-database-users-making-your-database-portable?view=azure-sqldw-latest&preserve-view=true)** | Igen. **Megjegyzés:** csak egy Azure ad-felhasználó lehet korlátlan rendszergazda | No |
| **SQL Felhasználónév/jelszó hitelesítése**| Igen | Yes |
| **Azure Active Directory (Azure AD) hitelesítés**| Igen, az Azure AD-felhasználók | Igen, Azure AD-bejelentkezések és felhasználók |
| **Storage Azure Active Directory (Azure AD) továbbító hitelesítés** | Igen | Yes |
| **Storage SAS-jogkivonat hitelesítése** | No | Igen, az [adatbázis-hatókörrel rendelkező hitelesítő adatok](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true) használata [külső adatforrásban](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true) vagy példány-szintű [hitelesítő](/sql/t-sql/statements/create-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true)adatban. |
| **Tárterület-hozzáférési kulcs hitelesítése** | Igen, [adatbázis-hatókörű hitelesítő](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true) adat használata a [külső adatforrásban](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true) | No |
| **Storage [felügyelt identitások](../security/synapse-workspace-managed-identity.md) hitelesítése** | Igen, [Managed Service Identity hitelesítő adat](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&preserve-view=true&toc=%2fazure%2fsynapse-analytics%2ftoc.json&view=azure-sqldw-latest&preserve-view=true) használatával | Igen, a `Managed Identity` hitelesítő adatok használatával. |
| **Storage-alkalmazás identitásának hitelesítése** | [Igen](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true) | No |
| **Engedélyek – objektum szintű** | Igen, beleértve a felhasználók engedélyeinek megadására, megtagadására és visszavonására vonatkozó képességet. | Igen, beleértve a felhasználók/bejelentkezések engedélyének ENGEDÉLYEZÉSét, megtagadását és visszavonását a támogatott rendszerobjektumokon |
| **Engedélyek – séma szintű** | Igen, beleértve a sémában a felhasználókra/bejelentkezésekre vonatkozó engedélyek megadásának, megtagadásának és visszavonásának képességét. | Igen, beleértve a sémában a felhasználókra/bejelentkezésekre vonatkozó engedélyek megadásának, megtagadásának és visszavonásának képességét. |
| **Engedélyek – [adatbázis szintű](/sql/relational-databases/security/authentication-access/database-level-roles?view=azure-sqldw-latest&preserve-view=true)** | Igen | Yes |
| **Engedélyek – [kiszolgáló szintű](/sql/relational-databases/security/authentication-access/server-level-roles)** | No | Igen, a sysadmin és más kiszolgálói szerepkörök támogatottak |
| **Engedélyek – [oszlop szintű biztonság](../sql-data-warehouse/column-level-security.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)** | Igen | Yes |
| **Szerepkörök/csoportok** | Igen (az adatbázis hatóköre) | Igen (a kiszolgáló és az adatbázis hatóköre is) |
| **Biztonsági &amp; azonosító függvények** | Egyes Transact-SQL biztonsági függvények és operátorok:,,,,  `CURRENT_USER` `HAS_DBACCESS` `IS_MEMBER` `IS_ROLEMEMBER` `SESSION_USER` , `SUSER_NAME` , `SUSER_SNAME` , `SYSTEM_USER` , `USER` , `USER_NAME` `EXECUTE AS` ,, `OPEN/CLOSE MASTER KEY` | Egyes Transact-SQL biztonsági függvények és operátorok:,,,,,,,,,,,,,  `CURRENT_USER` `HAS_DBACCESS` `HAS_PERMS_BY_NAME` `IS_MEMBER', 'IS_ROLEMEMBER` `IS_SRVROLEMEMBER` `SESSION_USER` `SESSION_CONTEXT` `SUSER_NAME` `SUSER_SNAME` `SYSTEM_USER` `USER` `USER_NAME` `EXECUTE AS` és `REVERT` . A biztonsági függvények nem használhatók külső adatok lekérdezésére (a lekérdezésben használható, változóban tárolt eredmény tárolására).  |
| **ADATBÁZIS-HATÓKÖRŰ HITELESÍTŐ ADAT** | Igen | Yes |
| **KISZOLGÁLÓ HATÓKÖRŰ HITELESÍTŐ ADATAI** | Nem | Igen |
| **Sorszintű biztonság** | [Igen](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | No |
| **Transzparens adattitkosítás (TDE)** | [Igen](../../azure-sql/database/transparent-data-encryption-tde-overview.md) | No | 
| **Adatfelderítés és -besorolás** | [Igen](../../azure-sql/database/data-discovery-and-classification-overview.md) | No |
| **Biztonsági rések felmérése** | [Igen](../../azure-sql/database/sql-vulnerability-assessment.md) | No |
| **Advanced Threat Protection** | [Igen](../../azure-sql/database/threat-detection-overview.md)
| **Naplózás** | [Igen](../../azure-sql/database/auditing-overview.md) | No |
| **[Tűzfalszabályok](../security/synapse-workspace-ip-firewall.md)**| Igen | Yes |
| **[Privát végpont](../security/synapse-workspace-managed-private-endpoints.md)**| Igen | Yes |

A dedikált SQL-készlet és a kiszolgáló nélküli SQL-készlet szabványos Transact-SQL nyelvet használ az adat lekérdezéséhez. A részletes különbségekért tekintse meg a [Transact-SQL nyelvi referenciáját](/sql/t-sql/language-reference).

## <a name="tools"></a>Eszközök

Az adatlekérdezéshez különféle eszközöket használhat a szinapszis SQL-hez való kapcsolódáshoz.

|   | Dedikált | Kiszolgáló nélküli |
| --- | --- | --- |
| **Synapse Studio** | Igen, SQL-parancsfájlok | Igen, SQL-parancsfájlok |
| **Power BI** | Yes | [Igen](tutorial-connect-power-bi-desktop.md) |
| **Azure Analysis Service** | Igen | Yes |
| **Azure Data Studio** | Yes | Igen, 1.18.0 vagy újabb verzió. Az SQL-parancsfájlok és az SQL-jegyzetfüzetek támogatottak. |
| **SQL Server Management Studio** | Yes | Igen, 18,5-es vagy újabb verzió |

> [!NOTE]
> A SSMS segítségével csatlakozhat a kiszolgáló nélküli SQL-készlethez és a lekérdezéshez. Részlegesen támogatott a 18,5-es verziótól kezdődően, csak a kapcsolódáshoz és a lekérdezésekhez használható.

Az alkalmazások többsége szabványos Transact-SQL nyelvet használ a szinapszis SQL dedikált és kiszolgáló nélküli használati modelljeinek lekérdezéséhez.

## <a name="storage-options"></a>Tárolási lehetőségek

Az elemzett adattípusok különböző tárolási típusok tárolására használhatók. A következő táblázat felsorolja az összes rendelkezésre álló tárolási beállítást:

|   | Dedikált | Kiszolgáló nélküli |
| --- | --- | --- |
| **Belső tárterület** | Igen | Nem |
| **Azure Data Lake v2** | Igen | Yes |
| **Azure Blob Storage** | Igen | Yes |
| **Azure SQL (távoli)** | Nem | Nem |
| **Azure CosmosDB tranzakciós tároló** | Nem | Nem |
| **Azure CosmosDB analitikai tároló** | No | Igen, a [szinapszis hivatkozás használatával (előzetes verzió)](../../cosmos-db/synapse-link.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) ([nyilvános előzetes](../../cosmos-db/synapse-link.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json#limitations)verzió) |
| **Táblák Apache Spark (a munkaterületen)** | No | PARKETTA-táblázatok csak [metaadat-szinkronizálás](develop-storage-files-spark-tables.md) használatával |
| **Apache Spark táblák (távoli)** | Nem | Nem |
| **Databricks táblák (távoli)** | Nem | Nem |

## <a name="data-formats"></a>Adatformátumok

Az elemzett adattárak különböző tárolási formátumokban tárolhatók. A következő táblázat felsorolja az összes elemezhető adatformátumot:

|   | Dedikált | Kiszolgáló nélküli |
| --- | --- | --- |
| **Tagolt** | [Igen](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) | [Igen](query-single-csv-file.md) |
| **CSV** | Igen (a több karakterből álló határolójelek nem támogatottak) | [Igen](query-single-csv-file.md) |
| **Parquet** | [Igen](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) | [Igen](query-parquet-files.md), beleértve a [beágyazott típusokkal](query-parquet-nested-types.md) rendelkező fájlokat is |
| **Kaptári ork** | [Igen](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) | No |
| **Struktúra RC** | [Igen](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) | No |
| **JSON** | Yes | [Igen](query-json-files.md) |
| **Avro** | Nem | Nem |
| **[Delta – Lake](https://delta.io/)** | Nem | Nem |
| **[CDM](/common-data-model/)** | Nem | Nem |

## <a name="next-steps"></a>Következő lépések
A dedikált SQL-készlet és a kiszolgáló nélküli SQL-készlet ajánlott eljárásaival kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Ajánlott eljárások a dedikált SQL-készlethez](best-practices-dedicated-sql-pool.md)
- [Ajánlott eljárások kiszolgáló nélküli SQL-készlethez](best-practices-serverless-sql-pool.md)
