---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 1ab404b838af65dcb75395dfeee1ca0553e497a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "67179789"
---
## <a name="specifying-structure-definition-for-rectangular-datasets"></a>A négyszögletű adatkészletek szerkezeti definíciójának megadása
Az adatkészletek JSON szerkezet szakasza egy **választható** szakasz a téglalap alakú táblákhoz (sorok & oszlopokkal), és a tábla oszlopainak gyűjteményét tartalmazza. A Structure (struktúra) szakaszt a típus konverzióhoz vagy az oszlop-hozzárendelések végrehajtásához szükséges információk biztosításához fogja használni. A következő szakaszok részletesen ismertetik ezeket a funkciókat. 

Minden oszlop a következő tulajdonságokat tartalmazza:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| name |Az oszlop neve. |Yes |
| típus |Az oszlop adattípusa. A típussal kapcsolatos információk megadásával kapcsolatos további részletekért tekintse meg az alábbi típusú konverziók szakaszt. |No |
| kulturális környezet |A típus megadásakor használandó .NET-alapú kulturális környezet, valamint a (m) típusú .net-típus datetime vagy DateTimeOffset. Az alapértelmezett érték az "en-us". |No |
| formátumban |A típus megadásakor használandó formázó karakterlánc, a típusa pedig a következő: dátum/idő vagy DateTimeOffset. |No |

Az alábbi minta egy olyan tábla JSON-szakaszát mutatja be, amelyben három oszlop felhasználóazonosító, név és lastlogindate található.

```json
"structure": 
[
    { "name": "userid"},
    { "name": "name"},
    { "name": "lastlogindate"}
],
```

Az alábbi útmutatást követve megtekintheti, hogy mikor szerepeljen a "Structure" információ, és mit kell belefoglalni a **struktúra** szakaszba.

* Az adatsémát és az adatokat tartalmazó **strukturált adatforrások esetében** (például SQL Server, Oracle, Azure Table stb.) a "Structure" (struktúra) szakaszt csak akkor kell megadnia, ha az adott forrásoldali oszlopok oszlopait a fogadó adott oszlopaihoz szeretné rendelni, és a nevük nem azonos (lásd az alábbi részleteket az oszlop-hozzárendelés szakaszban). 
  
    A fentiekben leírtak szerint a típus információi nem kötelezőek a "Structure" (struktúra) szakaszban. A strukturált források esetében az adattárban az adatkészlet definíciójának részeként az adatok már elérhetők, ezért nem szabad beírnia a típus adatait, ha a "Structure" (struktúra) szakaszt is tartalmazza.
* **Az olvasási adatforrásokra (kifejezetten az Azure blobra) vonatkozó séma esetében**  dönthet úgy, hogy az adatokat a séma vagy az adatok beírása nélkül tárolja. Az ilyen típusú adatforrások esetében a "Structure" kifejezést kell tartalmaznia a következő két esetben:
  * Oszlop-hozzárendelést szeretne végezni.
  * Ha az adatkészlet egy másolási tevékenység forrása, akkor megadhatja a "Structure" típusú adatokat, és a adat-előállító ezt a típusú információt fogja használni a fogadó natív típusainak átalakításához. További információért lásd az [adatok áthelyezése az Azure-ba és az Azure-blobból](../articles/data-factory/v1/data-factory-azure-blob-connector.md) című cikket.

### <a name="supported-net-based-types"></a>Támogatott. NET-alapú típusok
A adat-előállító a következő CLS-kompatibilis .NET-alapú típusú értékeket támogatja a (z) "Structure" típusú adatforrásokhoz, például az Azure blobhoz való hozzáféréshez.

* Int16
* Int32 
* Int64
* Egyirányú
* Dupla
* Tizedesjegy
* Bájt []
* Logikai
* Sztring 
* Guid
* Datetime
* DateTimeOffset
* Időtartomány 

A DateTime & DateTimeOffset esetében megadhatja a "kulturális" & "Format" karakterláncot is, amely megkönnyíti az egyéni datetime karakterlánc elemzését. Lásd az alábbi típusú átalakítás mintáját.

