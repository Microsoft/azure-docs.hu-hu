---
title: Fájlok elérése a tárolóban a kiszolgáló nélküli SQL-készletben
description: Leírja a tárolási fájlok lekérdezését a kiszolgáló nélküli SQL-készlettel az Azure szinapszis Analyticsben.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/19/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 679df881e928825496a03b2fef7c9374d61593f7
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2021
ms.locfileid: "103199988"
---
# <a name="access-external-storage-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Külső tárterület elérése kiszolgáló nélküli SQL-készlettel az Azure szinapszis Analyticsben

Ez a cikk azt ismerteti, hogy a felhasználók hogyan olvashatják el az Azure Storage-ban tárolt fájlokból származó információkat a kiszolgáló nélküli SQL-készletben. A felhasználók a következő beállításokkal férhetnek hozzá a tárolóhoz:

- [OpenRowset](develop-openrowset.md) függvény, amely alkalmi lekérdezéseket tesz lehetővé az Azure Storage-ban található fájlokon.
- Külső [tábla](develop-tables-external-tables.md) , amely a külső fájlok készletére épülő, előre definiált adatstruktúra.

A felhasználó [különböző hitelesítési módszereket](develop-storage-files-storage-access-control.md) használhat, például az Azure ad áteresztő hitelesítést (az alapértelmezett Azure ad-rendszerbiztonsági tag esetében) és az SAS-hitelesítést (az SQL-rendszerbiztonsági tag esetében alapértelmezés szerint).

## <a name="query-files-using-openrowset"></a>Fájlok lekérdezése a OPENROWSET használatával

A OPENROWSET lehetővé teszi a felhasználók számára az Azure Storage-beli külső fájlok lekérdezését, ha hozzáférnek a tárolóhoz. A kiszolgáló nélküli SQL-készlethez csatlakozó felhasználónak a következő lekérdezéssel kell elolvasnia az Azure Storage-ban tárolt fájlok tartalmát:

```sql
SELECT * FROM
 OPENROWSET(BULK 'https://<storage_account>.dfs.core.windows.net/<container>/<path>/*.parquet', format= 'parquet') as rows
```

A felhasználók a következő hozzáférési szabályok használatával férhetnek hozzá a tárolóhoz:

- Azure AD-felhasználó – a `OPENROWSET` hívó Azure ad-identitásával fér hozzá az Azure Storage szolgáltatáshoz, vagy hozzáférhet a tárhelyhez a névtelen hozzáféréssel.
- SQL-felhasználó – `OPENROWSET` a tárolót a névtelen hozzáféréssel érheti el, vagy megszemélyesítheti az SAS-jogkivonat vagy a munkaterület felügyelt identitása használatával.

### <a name="impersonation"></a>[Megszemélyesítési](#tab/impersonation)

Az SQL-rendszerbiztonsági tag a OPENROWSET használatával közvetlenül is lekérdezheti az SAS-tokenekkel vagy a munkaterület felügyelt identitásával védett fájlokat. Ha egy SQL-felhasználó végrehajtja ezt a függvényt, az engedéllyel rendelkező Kiemelt felhasználónak `ALTER ANY CREDENTIAL` létre kell hoznia egy kiszolgáló-hatókörű hitelesítő adatot, amely megfelel a függvényben lévő URL-címnek (a tároló nevét és tárolóját használva), és hivatkozásokat kapott a hitelesítő adatokhoz a OpenRowset függvény hívója számára:

```sql
EXECUTE AS somepoweruser

CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sas token';

GRANT REFERENCES ON CREDENTIAL::[https://<storage_account>.dfs.core.windows.net/<container>] TO sqluser
```

Ha nincs olyan kiszolgálói szintű HITELESÍTő adat, amely megfelel az URL-címnek, vagy az SQL-felhasználó nem rendelkezik hivatkozásokkal a hitelesítő adatokhoz, a rendszer a hibaüzenetet adja vissza. Az SQL-rendszerbiztonsági tag nem tud megszemélyesíteni néhány Azure AD-identitást.

### <a name="direct-access"></a>[Közvetlen hozzáférés](#tab/direct-access)

Nincs szükség további beállításra ahhoz, hogy az Azure AD-felhasználók identitásuk alapján hozzáférjenek a fájlokhoz.
Bármely felhasználó hozzáférhet az Azure Storage szolgáltatáshoz, amely lehetővé teszi a névtelen hozzáférést (további beállításra nincs szükség).

---

> [!NOTE]
> A OPENROWSET ezen verziója az alapértelmezett hitelesítéssel történő gyors és egyszerű adatelemzéshez készült. A megszemélyesítés vagy a felügyelt identitás kihasználása érdekében használja a következő szakaszban ismertetett adatforrással rendelkező OPENROWSET.

## <a name="query-data-sources-using-openrowset"></a>Adatforrások lekérdezése az OPENROWSET használatával

A OPENROWSET lehetővé teszi a felhasználó számára, hogy lekérdezze a külső adatforráson elhelyezett fájlokat:

```sql
SELECT * FROM
 OPENROWSET(BULK 'file/path/*.parquet',
 DATASOURCE = MyAzureInvoices,
 FORMAT= 'parquet') as rows
```

A lekérdezést végrehajtó felhasználónak képesnek kell lennie hozzáférni a fájlokhoz. A felhasználókat az [sas-jogkivonat](develop-storage-files-storage-access-control.md?tabs=shared-access-signature) vagy a [munkaterület felügyelt identitása](develop-storage-files-storage-access-control.md?tabs=managed-identity) használatával kell megszemélyesíteni, ha nem tudnak közvetlenül hozzáférni a fájlokhoz az [Azure ad-identitás](develop-storage-files-storage-access-control.md?tabs=user-identity) vagy a [Névtelen hozzáférés](develop-storage-files-storage-access-control.md?tabs=public-access)használatával.

### <a name="impersonation"></a>[Megszemélyesítési](#tab/impersonation)

`DATABASE SCOPED CREDENTIAL` Megadja, hogy miként lehet hozzáférni a hivatkozott adatforrásban található fájlokhoz (jelenleg SAS és felügyelt identitás). Az engedéllyel rendelkező energiaellátási felhasználónak `CONTROL DATABASE` létre kell hoznia `DATABASE SCOPED CREDENTIAL` ezt a tároló eléréséhez, `EXTERNAL DATA SOURCE` amely a használandó adatforrás és hitelesítő adatok URL-címét adja meg:

```sql
EXECUTE AS somepoweruser;

-- Create MASTER KEY if it doesn't exists in database
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'some very strong password';

CREATE DATABASE SCOPED CREDENTIAL AccessAzureInvoices
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&amp;sp=rwac&amp;se=2017-02-01T00:55:34Z&amp;st=201********' ;

CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>/' ,
 CREDENTIAL = AccessAzureInvoices) ;
```

A hívónak a következő engedélyek egyikével kell rendelkeznie a OPENROWSET függvény végrehajtásához:

- A OPENROWSET végrehajtásának egyik engedélye:
  - `ADMINISTER BULK OPERATIONS` engedélyezi a bejelentkezést a OPENROWSET függvény végrehajtásához.
  - `ADMINISTER DATABASE BULK OPERATIONS` lehetővé teszi, hogy az adatbázis hatókörű felhasználója OPENROWSET-függvényt hajtson végre.
- `REFERENCES DATABASE SCOPED CREDENTIAL` a-ben hivatkozott hitelesítő adathoz `EXTERNAL DATA SOURCE` .

### <a name="direct-access"></a>[Közvetlen hozzáférés](#tab/direct-access)

A felhasználó létrehozhat külső adatforrást olyan HITELESÍTő adatok nélkül, amely a nyilvános elérésű tárolóra hivatkozik, vagy az Azure AD átadó hitelesítést használja:

```sql
CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>') ;
```
---
## <a name="external-table"></a>KÜLSŐ TÁBLA

A táblázat olvasására jogosult felhasználók külső fájlokat is elérhet az Azure Storage-mappák és-fájlok készletén létrehozott külső tábla használatával.

A [külső tábla létrehozásához szükséges engedélyekkel](/sql/t-sql/statements/create-external-table-transact-sql?preserve-view=true&view=sql-server-ver15#permissions) rendelkező felhasználó (például CREATE TABLE és a hitelesítő adatok vagy az adatbázis HATÓKÖRű HITELESÍTŐ adatainak módosítása) a következő parancsfájllal hozhat létre táblázatot az Azure Storage-adatforráson:

```sql
CREATE EXTERNAL TABLE [dbo].[DimProductexternal]
( ProductKey int, ProductLabel nvarchar, ProductName nvarchar )
WITH
(
LOCATION='/DimProduct/year=*/month=*' ,
DATA_SOURCE = AzureDataLakeStore ,
FILE_FORMAT = TextFileFormat
) ;
```

A táblából adatokat olvasó felhasználónak el kell tudnia érni a fájlokat. A felhasználókat az [sas-jogkivonat](develop-storage-files-storage-access-control.md?tabs=shared-access-signature) vagy a [munkaterület felügyelt identitása](develop-storage-files-storage-access-control.md?tabs=managed-identity) használatával kell megszemélyesíteni, ha nem tudnak közvetlenül hozzáférni a fájlokhoz az [Azure ad-identitás](develop-storage-files-storage-access-control.md?tabs=user-identity) vagy a [Névtelen hozzáférés](develop-storage-files-storage-access-control.md?tabs=public-access)használatával.

### <a name="impersonation"></a>[Megszemélyesítési](#tab/impersonation)

Az adatbázis-HATÓKÖRrel rendelkező hitelesítő adatok a hivatkozott adatforráson lévő fájlok elérését határozzák meg. A CONTROL DATABASE engedéllyel rendelkező felhasználónak létre kell hoznia egy adatbázis-HATÓKÖRű HITELESÍTő adatot, amelyet a rendszer a tároló és a külső ADATFORRÁS eléréséhez fog használni, amely megadja az adatforrás és a hitelesítő adatok URL-címét:

```sql
EXECUTE AS somepoweruser;

-- Create MASTER KEY if it doesn't exists in database
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'some very strong password';

CREATE DATABASE SCOPED CREDENTIAL cred
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&sp=rwac&se=2017-02-01T00:55:34Z&st=201********' ;

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>' ,
 CREDENTIAL = cred
 ) ;
```

### <a name="direct-access"></a>[Közvetlen hozzáférés](#tab/direct-access)

A felhasználó létrehozhat külső adatforrást olyan HITELESÍTő adatok nélkül, amely a nyilvános elérésű tárolóra hivatkozik, vagy az Azure AD átadó hitelesítést használja:

```sql
CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>') ;
```
---

### <a name="read-external-files-with-external-table"></a>Külső fájlok beolvasása külső TÁBLÁZATtal

A külső tábla lehetővé teszi az adatok olvasását az adatforráson keresztül hivatkozott fájlokból a szabványos SQL SELECT utasítás használatával:

```sql
SELECT *
FROM dbo.DimProductsExternal
```

A hívónak az alábbi engedélyekkel kell rendelkeznie az információk olvasásához:
- `SELECT` engedély a külső táblán
- `REFERENCES DATABASE SCOPED CREDENTIAL` engedély `DATA SOURCE` , ha rendelkezik `CREDENTIAL`

## <a name="permissions"></a>Engedélyek

A következő táblázat a fent felsorolt műveletekhez szükséges engedélyeket sorolja fel.

| Lekérdezés | Szükséges engedélyek|
| --- | --- |
| OPENROWSET (BULK) adatforrás nélkül | `ADMINISTER BULK OPERATIONS`, `ADMINISTER DATABASE BULK OPERATIONS` vagy az SQL-bejelentkezéshez hivatkozásokkal rendelkező hitelesítő adatok szükségesek:: \<URL> sas által védett tároló esetén |
| OPENROWSET (TÖMEGES) adatforrással hitelesítő adatok nélkül | `ADMINISTER BULK OPERATIONS`vagy `ADMINISTER DATABASE BULK OPERATIONS` |
| OPENROWSET (TÖMEGES) adatforrással, hitelesítő adatokkal | `REFERENCES DATABASE SCOPED CREDENTIAL` és az egyik `ADMINISTER BULK OPERATIONS` vagy `ADMINISTER DATABASE BULK OPERATIONS` |
| KÜLSŐ ADATFORRÁS LÉTREHOZÁSA | `ALTER ANY EXTERNAL DATA SOURCE` és `REFERENCES DATABASE SCOPED CREDENTIAL` |
| KÜLSŐ TÁBLA LÉTREHOZÁSA | `CREATE TABLE`, `ALTER ANY SCHEMA` , `ALTER ANY EXTERNAL FILE FORMAT` és `ALTER ANY EXTERNAL DATA SOURCE` |
| KIVÁLASZTÁS KÜLSŐ TÁBLÁBÓL | `SELECT TABLE` és `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CETAS | Tábla létrehozása:, `CREATE TABLE` , `ALTER ANY SCHEMA` `ALTER ANY DATA SOURCE` , és `ALTER ANY EXTERNAL FILE FORMAT` . Az adat olvasása: `ADMINISTER BULK OPERATIONS` vagy a `REFERENCES CREDENTIAL` `SELECT TABLE` lekérdezés + R/W engedélyének minden táblája/nézete/funkciója a Storage-ban |

## <a name="next-steps"></a>Következő lépések

Most már készen áll a folytatásra a következő cikkekkel:

- [Adatlekérdezés a Storage szolgáltatásban](query-data-storage.md)

- [CSV-fájl lekérdezése](query-single-csv-file.md)

- [Parquet-fájlok lekérdezése](query-parquet-files.md)

- [JSON-fájlok lekérdezése](query-json-files.md)

- [Mappák és több fájl lekérdezése](query-folders-multiple-csv-files.md)

- [Particionálási és metaadat-függvények használata](query-specific-files.md)

- [Beágyazott típusok lekérdezése](query-parquet-nested-types.md)

- [Nézetek létrehozása és használata](create-use-views.md)
