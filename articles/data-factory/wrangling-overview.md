---
title: Az huzavona Azure Data Factory
description: A Azure Data Factory adathuzavona áttekintése
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: f922e7a2755a6e26a0d9f93f2668753e2f4dad5a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98738169"
---
# <a name="what-is-data-wrangling"></a>Mi az az adathuzavona?

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A szervezeteknek képesnek kell lenniük az adat-előkészítési és-huzavona kritikus fontosságú üzleti adataik feltárására, hogy pontos elemzést nyújtsanak a folyamatosan növekedő összetett adatmennyiségekről. Az adatelőkészítésre azért van szükség, hogy a szervezetek különböző üzleti folyamatok során használhassák az adatmennyiséget, és csökkentsék az időt az értékre.

A Data Factory lehetővé teszi, hogy a iteratív a Felhőbeli adatelőkészítést a Power Query használatával. A Data Factory a [Power Query online](/power-query/) szolgáltatással integrálódik, és Power Query M functions elérhetővé teszi a folyamat tevékenységként.

Data Factory lefordítja a Power Query online mashup-szerkesztő által generált Spark-kódra a Felhőbeli méretezés végrehajtásához az M-re Azure Data Factory adatfolyamatokra való lefordításával. Az Power Query-és adatfolyamatokkal rendelkező huzavona különösen hasznosak az adatmérnökök vagy az "állampolgári adatintegrátorok" számára.

> [!NOTE]
> A Azure Data Factory Power Query tevékenység jelenleg nyilvános előzetes verzióban érhető el

## <a name="use-cases"></a>Használati esetek

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFkW]

### <a name="fast-interactive-data-exploration-and-preparation"></a>Gyors interaktív adatfeltárás és-előkészítés

A több adatmérnökök és az állampolgárok adatintegrátorai interaktív módon tudják felderíteni és előkészíteni az adatkészleteket a Felhőbeli méretekben. Az adattavak mennyiségének, változatosságának és sebességének növekedésével a felhasználóknak hatékony módon kell megvizsgálniuk és előkészíteniük az adatkészleteket. Előfordulhat például, hogy létre kell hoznia egy adatkészletet, amely a "minden ügyfél demográfiai információt tartalmaz az új ügyfelek számára a 2017 óta". Nem egy ismert célhoz van hozzárendelve. Olyan adatkészleteket keres, huzavona és prepping, amelyek megfelelnek egy követelménynek, mielőtt közzéteszik azt a tóban. A huzavona gyakran használják kevésbé formális elemzési forgatókönyvek esetén. A elő-adatkészletek felhasználhatók átalakítások és gépi tanulási műveletek végrehajtásához.

### <a name="code-free-agile-data-preparation"></a>Kód nélküli agilis adatelőkészítés

Az állampolgárok adatintegrátorai több mint 60%-ot költenek az adatgyűjtésre és-előkészítésre. Azt szeretnék, hogy a működés hatékonyságának javítása érdekében kód nélküli módon végezzék el. Lehetővé teszi az adatbányászati adatintegrátorok számára, hogy az olyan ismert eszközökkel bővítsék, formálják és tegyenek közzé az információkat, mint például a Power Query online, ami jelentősen javítja a termelékenységet. A Azure Data Factory huzavona lehetővé teszi a jól ismert Power Query online adategyesítési szerkesztő számára, hogy lehetővé tegye az állampolgári adatintegrátorok számára a hibák gyors javítását, az adatszabványosítást és az üzleti döntések támogatásához szükséges magas színvonalú adatelemzést.

### <a name="data-validation-and-exploration"></a>Az adatellenőrzés és a feltárás

Az adatokat kód nélküli módon vizsgálhatja, hogy eltávolítsa a kiugró értékeket, a rendellenességeket, és megegyezzen a gyors elemzések egy alakzatával.

## <a name="supported-sources"></a>Támogatott források

| Összekötő | Adatformátum | Hitelesítéstípus |
| -- | -- | --|
| [Azure Blob Storage](connector-azure-blob-storage.md) | CSV, parketta | Fiók kulcsa |
| [1. generációs Azure Data Lake Storage](connector-azure-data-lake-store.md) | CSV | Szolgáltatásnév |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | CSV, parketta | Fiók kulcsa, egyszerű szolgáltatásnév |
| [Azure SQL Database](connector-azure-sql-database.md) | - | SQL-hitelesítés |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | SQL-hitelesítés |

## <a name="the-mashup-editor"></a>Az összemashup-szerkesztő

Power Query tevékenység létrehozásakor az összes forrás-adatkészlet adatkészlet-lekérdezésekvé válik, és a rendszer a **ADFResource** mappába helyezi őket. Alapértelmezés szerint a UserQuery az első adatkészlet-lekérdezésre mutat. Az összes átalakítást a UserQuery kell elvégezni, mivel az adatkészlet-lekérdezések módosításai nem támogatottak, és nem maradnak meg. A lekérdezések átnevezése, hozzáadása és törlése jelenleg nem támogatott.

![Huzavona](media/wrangling-data-flow/editor.png)

Jelenleg nem minden Power Query M függvény támogatott az adatok huzavona, annak ellenére, hogy a létrehozás során elérhetővé válik. A Power Query tevékenységek létrehozásakor a rendszer a következő hibaüzenetet fogja kérni, ha egy függvény nem támogatott:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

A támogatott átalakításokkal kapcsolatos további információkért lásd: [az adathuzavona függvények](wrangling-functions.md).

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [hozhat létre adathuzavona Power Query mash-up](wrangling-tutorial.md)használatával.
