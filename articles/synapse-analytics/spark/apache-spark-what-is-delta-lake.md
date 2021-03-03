---
title: Mi az a Delta Lake?
description: A Delta Lake áttekintése és az Azure szinapszis Analytics részeként való működése
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 23a269fe9498295a3ff325a261c6539f5e1e31aa
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676219"
---
# <a name="what-is-delta-lake"></a>Mi az a Delta Lake?

Az Azure szinapszis Analytics kompatibilis a Linux Foundation Delta Lake-szel. A Delta Lake egy nyílt forráskódú tárolási réteg, amely sav (atomi, konzisztencia, elkülönítés és tartósság) tranzakciókat hoz Apache Spark és big data munkaterhelések számára.

Az Azure szinapszis által tartalmazott Delta Lake aktuális verziója a Scala, a PySpark és a .NET nyelvi támogatását tartalmazza. A lap alján található hivatkozások részletesebb példákkal és dokumentációval rendelkeznek.

## <a name="key-features"></a>A legfontosabb jellemzők

| Szolgáltatás | Leírás |
| --- | --- |
| **SAVAS tranzakciók** | Az adattavak jellemzően több folyamaton és folyamaton keresztül vannak feltöltve, amelyek némelyike az olvasással párhuzamosan ír adatokat. A Delta Lake és a tranzakciók hozzáadását megelőzően az adatmérnököknek az adatok integritásának biztosítására szolgáló manuális hiba miatt kellett átesniük. A Delta Lake ismerős savas tranzakciókat biztosít az adattavak számára. Szerializált, az elkülönítési szint legerősebb szintjét biztosítja. Ismerkedjen meg [a Delta Lake-be: a tranzakciónapló kicsomagolásával](https://databricks.com/blog/2019/08/21/diving-into-delta-lake-unpacking-the-transaction-log.html).|
| **Méretezhető metaadatok kezelésére** | A big data még a metaadatok is lehetnek "big data". A Delta Lake az adatokhoz hasonlóan kezeli a metaadatokat, és kihasználja a Spark elosztott feldolgozási teljesítményét az összes metaadatának kezeléséhez. Ennek eredményeképpen a Delta Lake könnyedén kezelheti a több milliárd partíciót és fájlt tartalmazó petabyte táblázatokat. |
| **Utazási idő (adatverziószámozás)** | A tranzakciók egyik fő funkciója a "Visszavonás", illetve a korábbi verzióra való visszalépés lehetősége. A Delta-tó olyan adatpillanatképeket biztosít, amelyek lehetővé teszik a korábbi adatverziók visszaállítását a vizsgálatok, a visszagörgetések vagy a kísérletek reprodukálása céljából. További információ a [Delta Lake Time Travel nagy léptékű Adattavakhoz való bevezetéséről](https://databricks.com/blog/2019/02/04/introducing-delta-time-travel-for-large-scale-data-lakes.html). |
| **Nyílt formátum** | Az Apache Parquet a Delta Lake alapformátuma, amely lehetővé teszi a formátumra natív, hatékony tömörítési és kódolási sémák kihasználása. |
| **Egyesített batch és streaming forrás és fogadó** | A Delta Lake egyik táblája a Batch-tábla, valamint a streaming forrás és a fogadó. Az adatfolyamok betöltését, a Batch-backfill és az interaktív lekérdezéseket csak a dobozból kell kidolgozni. |
| **Séma kényszerítése** | A séma kényszerítése révén biztosítható, hogy az adattípusok helyesek és kötelező oszlopok legyenek, ami megakadályozza, hogy az adatok inkonzisztenciát okozzanak. További információkért lásd [: a változás a Delta Lake-ben: a séma kényszerítése & evolúciója](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **Séma-evolúció** | A Delta Lake lehetővé teszi egy olyan táblázatos séma módosítását, amely automatikusan alkalmazható, anélkül, hogy az áttelepítési DDL-t kellene írnia. További információkért lásd [: a változás a Delta Lake-ben: a séma kényszerítése & evolúciója](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **Naplózási előzmények** | A Delta Lake Transaction napló rögzíti a módosítások teljes naplózási nyomvonalát biztosító adatokon végrehajtott összes módosítás részleteit. |
| **Frissítések és törlések** | A Delta Lake számos funkció esetében támogatja a Scala/Java/Python és az SQL API-kat. Az egyesítési, frissítési és törlési műveletek támogatása segíti a megfelelőségi követelmények teljesítését. További információkért lásd: [a Delta Lake 0.6.1 kiadásának bejelentése](https://delta.io/news/delta-lake-0-6-1-released/),  [bejelenti a Delta Lake 0,7 kiadását](https://delta.io/news/delta-lake-0-7-0-released/) és [egyszerű, megbízható Upsert, és törli a különbözeti Lake-táblákat a Python API](https://databricks.com/blog/2019/10/03/simple-reliable-upserts-and-deletes-on-delta-lake-tables-using-python-apis.html)-k használatával, amely tartalmazza a DML-parancsok egyesítéséhez, frissítéséhez és törléséhez szükséges kódrészleteket. |
| **100%-kompatibilis a Apache Spark API-val** | A fejlesztők a meglévő adatfolyamatokkal és a meglévő Spark-implementációkkal teljes mértékben kompatibilis módon használhatják a Delta Lake-et. |

A teljes dokumentációért lásd a [Delta Lake dokumentációs oldalát](https://docs.delta.io/latest/delta-intro.html) .

További információ: [Delta Lake Project](https://github.com/delta-io/delta).

## <a name="next-steps"></a>Következő lépések

- [.NET Apache Spark dokumentációhoz](/dotnet/spark)
- [Azure Synapse Analytics](../index.yml)