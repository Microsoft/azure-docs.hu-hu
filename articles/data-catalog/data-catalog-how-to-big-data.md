---
title: big data katalogizálása Azure Data Catalog
description: Útmutató a Azure Data Catalog "big data" adatforrásokkal, például az Azure Blob Storage, a Azure Data Lake és a Hadoop-HDFS való használatának mintáinak kiemeléséhez.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 3e898542c25f6ed1a9e6c811ada47220733abb12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674665"
---
# <a name="how-to-catalog-big-data-in-azure-data-catalog"></a>big data katalogizálása Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Bevezetés

A **Microsoft Azure Data Catalog** egy teljes körűen felügyelt felhőalapú szolgáltatás, amely a vállalati adatforrások regisztrálási és felderítési rendszereként szolgál. Arról is szól, hogy segítsen az embereknek felderíteni, megérteni és használni az adatforrásokat, és segít a szervezeteknek a meglévő adatforrások, például a big data további értékének megszerzésében.

**Azure Data Catalog** támogatja az Azure Storage-blobok és-könyvtárak regisztrációját, valamint a Hadoop HDFS-fájljait és-könyvtárait. Az adatforrások részben strukturált jellege nagy rugalmasságot biztosít. Ahhoz azonban, hogy a lehető legtöbbet lehessen regisztrálni a **Azure Data Catalog**, a felhasználóknak meg kell fontolniuk az adatforrások rendszerezését.

## <a name="directories-as-logical-data-sets"></a>Könyvtárak logikai adathalmazként

Big data források rendszerezésének közös mintája a címtárak logikai adathalmazként való kezelése. A legfelső szintű címtárak használatával határozható meg az adathalmazok, míg az almappák meghatározzák a partíciókat, és a bennük tárolt fájlok magukban foglalják az adattárolást.

Ilyen minta például a következő lehet:

```text
    \vehicle_maintenance_events
        \2013
        \2014
        \2015
            \01
                \2015-01-trailer01.csv
                \2015-01-trailer92.csv
                \2015-01-canister9635.csv
                ...
    \location_tracking_events
        \2013
        ...
```

Ebben a példában a vehicle_maintenance_events és a location_tracking_events logikai adatkészleteket képvisel. Ezen mappák mindegyike az év és a hónap almappáiba rendezett adatfájlokat tartalmazza. Ezek a mappák több száz vagy akár több ezer fájlt is tartalmazhatnak.

Ebben a mintában az egyes fájlok regisztrálása **Azure Data Catalog** valószínűleg nem lenne értelme. Ehelyett regisztrálja azokat az adatkészleteket képviselő címtárakat, amelyek az adattal dolgozó felhasználók számára hasznosak.

## <a name="reference-data-files"></a>Hivatkozási adatfájlok

A kiegészítő minta a hivatkozási adatkészletek különálló fájlként való tárolása. Ezek az adatkészletek a big data "kis" oldalára utalnak, és gyakran hasonlítanak az analitikus adatmodellek méreteihez. A hivatkozási adatfájlok olyan rekordokat tartalmaznak, amelyek a big data tárolóban máshol tárolt adatfájlok nagy részét képező kontextus biztosítására szolgálnak.

Ilyen minta például a következő lehet:

```text
    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv
```

Ha egy elemző vagy adattudós együttműködik a nagyobb címtár-struktúrákban található adatokkal, az ezekben a hivatkozásokban szereplő adatokkal részletesebb információkat adhat meg azokról az entitásokról, amelyeket a nagyobb adatkészletek csak névvel vagy AZONOSÍTÓval neveznek.

Ebben a mintában érdemes regisztrálni az egyes hivatkozási adatfájlokat **Azure Data Catalog**. Minden fájl egy adathalmazt jelöl, és mindegyiket külön lehet jegyzetbe adni és felderíteni.

## <a name="alternate-patterns"></a>Alternatív minták

Az előző szakaszban ismertetett minták csak két lehetséges módon rendezhetők big data tárolóba, de az egyes implementációk eltérőek. Függetlenül attól, hogy az adatforrások hogyan vannak strukturálva, big data-források regisztrálásakor **Azure Data Catalog**, a szervezeten belül mások számára értékű adathalmazokat jelölő fájlok és könyvtárak regisztrálására koncentráljon. Az összes fájl és könyvtár regisztrálása rendetlenséget teremt a katalógusban, így megnehezíti a felhasználók számára a szükséges igények megtalálását.

## <a name="summary"></a>Összefoglalás

Az adatforrások **Azure Data Catalog** való regisztrálása megkönnyíti a felderítését és megismerését. A logikai adatkészleteket jelképező big data fájlok és könyvtárak regisztrálásával és megjegyzésével segítséget nyújthat a felhasználóknak a számukra szükséges big data-források megtalálásában és használatában.
