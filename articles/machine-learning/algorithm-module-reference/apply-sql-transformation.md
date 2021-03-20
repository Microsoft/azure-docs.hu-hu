---
title: SQL-átalakítás alkalmazása
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használható az SQL-transzformációs modul alkalmazása Azure Machine Learning egy SQLite-lekérdezés futtatásához a bemeneti adatkészleteken az adatok átalakításához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/12/2020
ms.openlocfilehash: c66fbe59fd5b2660d02bfca285f78666d64569fe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94555600"
---
# <a name="apply-sql-transformation"></a>SQL-átalakítás alkalmazása

Ez a cikk a Azure Machine Learning Designer modulját ismerteti.

Az SQL-transzformációs modul alkalmazásával a következőket végezheti el:
  
-   Hozzon létre táblázatokat az eredményekhez, és mentse az adatkészleteket egy hordozható adatbázisban.  
  
-   Egyéni átalakításokat végezhet az adattípusokon, vagy létrehozhat összesítéseket.  
  
-   SQL-lekérdezési utasítások végrehajtása az adatszűréshez és az adatmódosításhoz, valamint a lekérdezés eredményének adattáblaként való visszaadása.  

> [!IMPORTANT]
> Az ebben a modulban használt SQL-motor **SQLite**. Az SQLite szintaxissal kapcsolatos további információkért lásd az SQL-et az [SQLite által értelmezett módon](https://www.sqlite.org/index.html).
> Ez a modul a memóriában lévő SQLite-ba ütközik, így a modul végrehajtása sokkal több memóriát igényel, és `Out of memory` hibát okozhat. Győződjön meg arról, hogy a számítógépén elegendő RAM van.

## <a name="how-to-configure-apply-sql-transformation"></a>Az SQL-transzformáció alkalmazásának konfigurálása  

A modul bemenetként akár három adatkészletet is igénybe vehet. Ha az egyes bemeneti portokhoz kapcsolódó adatkészletekre hivatkozik, akkor a és a nevet kell használnia `t1` `t2` `t3` . A tábla száma a bemeneti port indexét jelzi.  

Az alábbi mintakód bemutatja, hogyan csatlakozhat két táblához. a T1 és a T2 két olyan adathalmaz, amely a bal és a középső bemeneti porton csatlakozik az **SQL-transzformáció alkalmazásához**:

```sql
SELECT t1.*
    , t3.Average_Rating
FROM t1 join
    (SELECT placeID
        , AVG(rating) AS Average_Rating
    FROM t2
    GROUP BY placeID
    ) as t3
on t1.placeID = t3.placeID
```
  
A fennmaradó paraméter egy SQL-lekérdezés, amely az SQLite szintaxist használja. Ha több sort ír be az **SQL-parancsfájl** szövegmezőbe, használjon pontosvesszőt az egyes utasítások megszakításához. Ellenkező esetben a sortörések szóközökre lesznek konvertálva.  

Ez a modul az SQLite szintaxis összes szabványos utasítását támogatja. A nem támogatott utasítások listáját a [technikai megjegyzések](#technical-notes) című szakaszban találja.

##  <a name="technical-notes"></a>Technikai megjegyzések  

Ez a szakasz megvalósítási részleteket, tippeket és válaszokat tartalmaz a gyakori kérdésekre.

-   Az 1. porton mindig szükség van egy bemenetre.  
  
-   Szóközt vagy más speciális karaktereket tartalmazó oszlopok azonosítói esetén az oszlop azonosítóját szögletes zárójelben vagy dupla idézőjelben kell megadni, amikor a vagy a záradékban lévő oszlopra hivatkozik `SELECT` `WHERE` .  
  
### <a name="unsupported-statements"></a>Nem támogatott utasítások  

Bár az SQLite az ANSI SQL standard nagy részét is támogatja, nem tartalmazza számos, a kereskedelmi kapcsolatban álló adatbázis-rendszerek által támogatott funkciót. További információ: az [SQL mint az SQLite által értelmezett](http://www.sqlite.org/lang.html). Emellett vegye figyelembe a következő korlátozásokat az SQL-utasítások létrehozásakor:  
  
- A SQLite az értékek dinamikus beírását használja ahelyett, hogy egy típust egy oszlophoz társítson, mint a legtöbb kapcsolódó adatbázis-rendszerben. Ez gyenge módon van beírva, és lehetővé teszi az implicit típus-átalakítást.  
  
- `LEFT OUTER JOIN` implementálva van, de nem `RIGHT OUTER JOIN` vagy `FULL OUTER JOIN` .  

- `RENAME TABLE` `ADD COLUMN` A paranccsal és a utasításokkal is használható `ALTER TABLE` , de más záradékok nem támogatottak, például: `DROP COLUMN` , `ALTER COLUMN` és `ADD CONSTRAINT` .  
  
- Létrehozhat egy nézetet SQLite-n belül, de ezt követően a nézetek csak olvashatók. Nézeten nem hajtható végre `DELETE` , `INSERT` vagy `UPDATE` utasítás. Létrehozhat azonban egy olyan eseményindítót, amely a (z) vagy a (z) vagy a (z) rendszerre irányuló kísérletet `DELETE` `INSERT` `UPDATE` hajt végre, és az eseményindító törzsében más műveleteket is végrehajt.  
  

A hivatalos SQLite webhelyen elérhető nem támogatott függvények listája mellett a következő wiki a többi nem támogatott funkció listáját tartalmazza: [SQLite –](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql) nem támogatott SQL  
    
## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
