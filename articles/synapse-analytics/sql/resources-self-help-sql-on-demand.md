---
title: Kiszolgáló nélküli SQL-készlet önkiszolgáló súgója
description: Ez a szakasz olyan információkat tartalmaz, amelyek segíthetnek a kiszolgáló nélküli SQL-készlettel kapcsolatos problémák elhárításában.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: f94134b2a06155b1b1f390175578e501a840038b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101669418"
---
# <a name="self-help-for-serverless-sql-pool"></a>Önkiszolgáló nélküli SQL-készlet – Súgó

Ez a cikk azt ismerteti, hogyan lehet elhárítani a leggyakoribb problémákat a kiszolgáló nélküli SQL-készlettel az Azure szinapszis Analyticsben.

## <a name="serverless-sql-pool-is-grayed-out-in-synapse-studio"></a>A kiszolgáló nélküli SQL-készlet szürkén jelenik meg a szinapszis Studióban

Ha a szinapszis Studio nem tud kapcsolatot létesíteni a kiszolgáló nélküli SQL-készlettel, megfigyelheti, hogy a kiszolgáló nélküli SQL-készlet szürkén jelenik meg, vagy "offline" állapotot jelenít meg. Ez a probléma általában akkor fordul elő, ha a következő esetek valamelyike történik:

1) A hálózat megakadályozza az Azure szinapszis-háttérrel való kommunikációt. A legtöbb esetben a 1443-as port le van tiltva. A kiszolgáló nélküli SQL-készlet működésének megkezdéséhez tiltsa le a portot. Más problémák miatt előfordulhat, hogy a kiszolgáló nélküli SQL-készlet is működik, [További információért látogasson el a teljes hibaelhárítási útmutatóba](../troubleshoot/troubleshoot-synapse-studio.md).
2) Nincs engedélye a kiszolgáló nélküli SQL-készletbe való bejelentkezésre. A hozzáféréshez az Azure Synapse-munkaterület egyik rendszergazdájának hozzá kell adnia Önt a munkaterület-rendszergazda vagy az SQL-rendszergazda szerepkörhöz. [További információért tekintse meg a hozzáférés-vezérlés teljes körű útmutatóját](../security/synapse-workspace-access-control-overview.md).

## <a name="query-fails-because-file-cannot-be-opened"></a>A lekérdezés sikertelen, mert a fájl nem nyitható meg

Ha a lekérdezés sikertelen a "fájl nem nyitható meg, mert nem létezik, vagy egy másik folyamat használja", és biztos benne, hogy mindkét fájl létezik, és egy másik folyamat nem használja azt, az azt jelenti, hogy a kiszolgáló nélküli SQL-készlet nem fér hozzá a fájlhoz. Ez a probléma általában azért fordul elő, mert a Azure Active Directory identitása nem rendelkezik jogosultsággal a fájl eléréséhez. Alapértelmezés szerint a kiszolgáló nélküli SQL-készlet a Azure Active Directory identitásával próbál hozzáférni a fájlhoz. A probléma megoldásához megfelelő jogosultságokkal kell rendelkeznie a fájl eléréséhez. Ennek legegyszerűbb módja, ha „Storage-blobadatok közreműködője” szerepkört ad saját magának ahhoz a tárterülethez, amelyet lekérdezni próbál. 
- [További információért tekintse meg az Azure Active Directory teljes körű tárterülethozzáférés-vezérlési útmutatóját](../../storage/common/storage-auth-aad-rbac-portal.md). 
- [A kiszolgáló nélküli SQL-készlethez tartozó hozzáférés-vezérlési fiók elérésének megkeresése az Azure szinapszis Analyticsben](develop-storage-files-storage-access-control.md)

## <a name="query-fails-because-it-cannot-be-executed-due-to-current-resource-constraints"></a>A lekérdezés sikertelen, mert a jelenlegi erőforrás-megkötések miatt nem hajtható végre. 

Ha a lekérdezés sikertelen, a következő hibaüzenet jelenik meg: "Ez a lekérdezés nem hajtható végre az aktuális erőforrás-korlátozások miatt", az azt jelenti, hogy a kiszolgáló nélküli SQL-készlet nem tudja végrehajtani az adott pillanatban az erőforrás-megkötések miatt. 

- Győződjön meg arról, hogy megfelelő méretű adattípusok vannak használatban. Emellett a karakterlánc-oszlopokhoz tartozó Parquet-fájlok sémáját is megadhatja, mivel alapértelmezés szerint VARCHAR (8000) lesz. 

- Ha a lekérdezés CSV-fájlokat céloz meg, érdemes lehet [statisztikai adatokat létrehoznia](develop-tables-statistics.md#statistics-in-serverless-sql-pool). 

- A lekérdezés optimalizálásához látogasson el [a kiszolgáló nélküli SQL-készlet teljesítményének bevált eljárásaira](best-practices-sql-on-demand.md) .  

## <a name="create-statement-is-not-supported-in-master-database"></a>A CREATE "utasítás" nem támogatott a Master adatbázisban

Ha a lekérdezés nem sikerül, a következő hibaüzenet jelenik meg:

> ' A lekérdezés végrehajtása sikertelen. Hiba: külső tábla/ADATFORRÁS/adatbázis-HATÓKÖRű HITELESÍTő adat/FÁJLFORMÁTUM létrehozása nem támogatott a Master adatbázisban. " 

Ez azt jelenti, hogy a kiszolgáló nélküli SQL-készletben lévő Master adatbázis nem támogatja a következő létrehozását:
  - Külső táblák
  - Külső adatforrások
  - Adatbázis-hatókörrel rendelkező hitelesítő adatok
  - Külső fájlformátumok

Megoldás:

  1. Hozzon létre egy felhasználói adatbázist:

```sql
CREATE DATABASE <DATABASE_NAME>
```

  2. A Create utasítás végrehajtása <DATABASE_NAME> környezetében, amely korábban a Master adatbázis esetében meghiúsult. 
  
  Példa külső fájlformátum létrehozására:
    
```sql
USE <DATABASE_NAME>
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] 
WITH ( FORMAT_TYPE = PARQUET)
```

## <a name="next-steps"></a>Következő lépések

Tekintse át a következő cikkeket, amelyekből többet tudhat meg a kiszolgáló nélküli SQL-készlet használatáról:

- [Egyetlen CSV-fájl lekérdezése](query-single-csv-file.md)

- [Mappák és több CSV-fájl lekérdezése](query-folders-multiple-csv-files.md)

- [Adott fájlok lekérdezése](query-specific-files.md)

- [Parquet-fájlok lekérdezése](query-parquet-files.md)

- [A Parquet beágyazott típusainak lekérdezése](query-parquet-nested-types.md)

- [JSON-fájlok lekérdezése](query-json-files.md)

- [Nézetek létrehozása és használata](create-use-views.md)

- [Külső táblák létrehozása és használata](create-use-external-tables.md)

- [Lekérdezés eredményeinek tárolása a tárterületen](create-external-table-as-select.md)
