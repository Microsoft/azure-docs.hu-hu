---
title: A Azure Data Factory díjszabásának ismertetése példákkal
description: Ez a cikk ismerteti és bemutatja a Azure Data Factory díjszabási modellt részletes példákkal
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/14/2020
ms.openlocfilehash: 3bb9574c74aaa3c2589d0ca93fb906168ca99095
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783370"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>A Data Factory díjszabásának ismertetése példákon keresztül

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk ismerteti és bemutatja a Azure Data Factory díjszabási modellt részletes példákkal.

> [!NOTE]
> Az alábbi példákban használt árak feltételezettek, és nem jelentenek tényleges díjszabást.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>Adatok másolása az AWS S3-ból az Azure Blob Storage-ba óránként

Ebben az esetben az AWS S3-ról az Azure Blob Storage-ba történő Adatmásolást óránkénti időpontra szeretné másolni.

A forgatókönyv végrehajtásához létre kell hoznia egy folyamatot a következő elemekkel:

1. Egy másolási tevékenység egy bemeneti adatkészlettel az AWS S3-ból másolandó adatokhoz.

2. Az Azure Storage-ban tárolt adat kimeneti adatkészlete.

3. Egy ütemezett trigger a folyamat óránkénti végrehajtásához.

   ![A diagramon egy ütemezett triggerrel rendelkező folyamat látható. A folyamat során a másolási tevékenység egy bemeneti adatkészletbe kerül, amely egy W S S3 társított szolgáltatásra és másolási tevékenységre áramlik, és egy kimeneti adatkészletbe is áramlik, amely egy Azure Storage-beli társított szolgáltatásba áramlik.](media/pricing-concepts/scenario1.png)

| **Műveletek** | **Típusok és egységek** |
| --- | --- |
| Társított szolgáltatás létrehozása | 2 olvasási/írási entitás  |
| Adatkészletek létrehozása | 4 írási/olvasási entitások (2 az adatkészlet létrehozásához, 2 a társított szolgáltatási referenciák esetében) |
| Folyamat létrehozása | 3 olvasási/írási entitás (1 a folyamat létrehozásához, 2 az adatkészlet-hivatkozásokhoz) |
| Folyamat beolvasása | 1 olvasási/írási entitás |
| Folyamat futtatása | 2 tevékenység fut (1 a trigger futtatásához, 1 a tevékenység futtatásához) |
| Adatok másolása feltételezés: végrehajtási idő = 10 perc | 10 \* 4 Azure Integration Runtime (alapértelmezett DIU-beállítás = 4) az adatintegrációs egységekkel és a másolási teljesítmény optimalizálásával kapcsolatos további információkért tekintse meg [ezt a cikket.](copy-activity-performance.md) |
| Figyelő folyamat feltételezése: csak 1 Futtatás történt | 2 a figyelő futtatási rekordjainak beolvasása (1 a folyamat futtatásához, 1 a tevékenység futtatásához) |

**Forgatókönyvek teljes díjszabása: $0,16811**

- Data Factory műveletek = **$0,0001**
  - Olvasás/írás = 10 \* 00001 = $0,0001 [1 R/W = $0,50/50000 = 0,00001]
  - Figyelés = 2 \* 000005 = $0,00001 [1 figyelés = $0,25/50000 = 0,000005]
- Folyamat-előkészítési &amp; végrehajtás = **$0,168**
  - Tevékenység-futtatások = 001 \* 2 = 0,002 [1 Futtatás = $1/1000 = 0,001]
  - Adatáthelyezési tevékenységek = $0,166 (a végrehajtási idő 10 perce arányban. 0,25 USD/óra Azure Integration Runtime)

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>Az Adatmásolás és az átalakítás Azure Databricks óránként

Ebben az esetben az AWS S3-ból az Azure Blob Storage-ba másolhatja az adatok adatait, és a Azure Databricks óránként át kell alakítani.

A forgatókönyv végrehajtásához létre kell hoznia egy folyamatot a következő elemekkel:

1. Egy másolási tevékenység bemeneti adatkészlettel az AWS S3-ból másolt adatokhoz, valamint az Azure Storage-beli adatok kimeneti adatkészlete.
2. Egy Azure Databricks tevékenység az adatátalakításhoz.
3. Egy ütemezett trigger a folyamat óránkénti végrehajtásához.

![A diagramon egy ütemezett triggerrel rendelkező folyamat látható. A folyamat során a másolási tevékenység egy bemeneti adatkészletbe, egy kimeneti adatkészletbe és egy DataBricks tevékenységbe kerül, amely Azure Databricks fut. A bemeneti adatkészlet egy W S S3 társított szolgáltatásba áramlik. A kimeneti adatkészlet egy Azure Storage-beli társított szolgáltatásba áramlik.](media/pricing-concepts/scenario2.png)

| **Műveletek** | **Típusok és egységek** |
| --- | --- |
| Társított szolgáltatás létrehozása | 3 olvasási/írási entitás  |
| Adatkészletek létrehozása | 4 írási/olvasási entitások (2 az adatkészlet létrehozásához, 2 a társított szolgáltatási referenciák esetében) |
| Folyamat létrehozása | 3 olvasási/írási entitás (1 a folyamat létrehozásához, 2 az adatkészlet-hivatkozásokhoz) |
| Folyamat beolvasása | 1 olvasási/írási entitás |
| Folyamat futtatása | 3 tevékenység fut (1 a trigger futtatásához, 2 a tevékenység-futtatásokhoz) |
| Adatok másolása feltételezés: végrehajtási idő = 10 perc | 10 \* 4 Azure Integration Runtime (alapértelmezett DIU-beállítás = 4) az adatintegrációs egységekkel és a másolási teljesítmény optimalizálásával kapcsolatos további információkért tekintse meg [ezt a cikket.](copy-activity-performance.md) |
| Figyelő folyamat feltételezése: csak 1 Futtatás történt | 3 a felügyeleti futtatási rekordok beolvasása (1 a folyamat futtatásához, 2 a tevékenység futtatásához) |
| Databricks tevékenység végrehajtása – feltételezés: végrehajtási idő = 10 perc | 10 perc külső folyamat tevékenységének végrehajtása |

**Forgatókönyvek teljes díjszabása: $0,16916**

- Data Factory műveletek = **$0,00012**
  - Írás/írás = 11 \* 00001 = $0,00011 [1 R/W = $0,50/50000 = 0,00001]
  - Monitoring = 3 \* 000005 = $0,00001 [1 figyelés = $0,25/50000 = 0,000005]
- Folyamat-előkészítési &amp; végrehajtás = **$0,16904**
  - Tevékenység-futtatások = 001 \* 3 = 0,003 [1 Futtatás = $1/1000 = 0,001]
  - Adatáthelyezési tevékenységek = $0,166 (a végrehajtási idő 10 perce arányban. 0,25 USD/óra Azure Integration Runtime)
  - Külső folyamat tevékenysége = $0,000041 (a végrehajtási idő 10 perce arányban. $0.00025/óra Azure Integration Runtime)

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>Adatok másolása és átalakítása dinamikus paraméterekkel óránként

Ebben a forgatókönyvben az AWS S3-ról az Azure Blob Storage-ba történő adatmásolásra és a Azure Databricks (a szkript dinamikus paramétereinek használatával) történő átalakítására van szükség óránkénti ütemterv szerint.

A forgatókönyv végrehajtásához létre kell hoznia egy folyamatot a következő elemekkel:

1. Egy másolási tevékenység egy bemeneti adatkészlettel az AWS S3-ból másolt adatokhoz, az Azure Storage-beli adatok kimeneti adatkészlete.
2. Egy keresési tevékenység, amely dinamikusan továbbítja a paramétereket az átalakítási parancsfájlba.
3. Egy Azure Databricks tevékenység az adatátalakításhoz.
4. Egy ütemezett trigger a folyamat óránkénti végrehajtásához.

![A diagramon egy ütemezett triggerrel rendelkező folyamat látható. A folyamat során a másolási tevékenység egy bemeneti adatkészletbe, egy kimeneti adatkészletbe és keresési tevékenységbe áramlik, amely DataBricks tevékenységre irányul, amely Azure Databricks fut. A bemeneti adatkészlet egy W S S3 társított szolgáltatásba áramlik. A kimeneti adatkészlet egy Azure Storage-beli társított szolgáltatásba áramlik.](media/pricing-concepts/scenario3.png)

| **Műveletek** | **Típusok és egységek** |
| --- | --- |
| Társított szolgáltatás létrehozása | 3 olvasási/írási entitás  |
| Adatkészletek létrehozása | 4 írási/olvasási entitások (2 az adatkészlet létrehozásához, 2 a társított szolgáltatási referenciák esetében) |
| Folyamat létrehozása | 3 olvasási/írási entitás (1 a folyamat létrehozásához, 2 az adatkészlet-hivatkozásokhoz) |
| Folyamat beolvasása | 1 olvasási/írási entitás |
| Folyamat futtatása | 4 tevékenység fut (1 a trigger futtatásához, 3 a tevékenység futtatásához) |
| Adatok másolása feltételezés: végrehajtási idő = 10 perc | 10 \* 4 Azure Integration Runtime (alapértelmezett DIU-beállítás = 4) az adatintegrációs egységekkel és a másolási teljesítmény optimalizálásával kapcsolatos további információkért tekintse meg [ezt a cikket.](copy-activity-performance.md) |
| Figyelő folyamat feltételezése: csak 1 Futtatás történt | 4 a figyelő futtatási rekordjainak beolvasása (1 a folyamat futtatásához, 3 a tevékenység futtatásához) |
| Keresési tevékenység végrehajtási feltételezése: végrehajtási idő = 1 perc | 1 perc folyamat tevékenységének végrehajtása |
| Databricks tevékenység végrehajtása – feltételezés: végrehajtási idő = 10 perc | 10 perc külső folyamat tevékenységének végrehajtása |

**Forgatókönyvek teljes díjszabása: $0,17020**

- Data Factory műveletek = **$0,00013**
  - Írás/írás = 11 \* 00001 = $0,00011 [1 R/W = $0,50/50000 = 0,00001]
  - Monitoring = 4 \* 000005 = $0,00002 [1 figyelés = $0,25/50000 = 0,000005]
- Folyamat-előkészítési &amp; végrehajtás = **$0,17007**
  - Tevékenység-futtatások = 001 \* 4 = 0,004 [1 Futtatás = $1/1000 = 0,001]
  - Adatáthelyezési tevékenységek = $0,166 (a végrehajtási idő 10 perce arányban. 0,25 USD/óra Azure Integration Runtime)
  - Folyamat aktivitása = $0,00003 (a végrehajtási idő 1 perces elszámolása. $0.002/óra Azure Integration Runtime)
  - Külső folyamat tevékenysége = $0,000041 (a végrehajtási idő 10 perce arányban. $0.00025/óra Azure Integration Runtime)

## <a name="using-mapping-data-flow-debug-for-a-normal-workday"></a>A leképezési adatfolyam hibakeresésének használata normál munkanapokon

A Sam feladata, hogy minden nap megtervezze, kiépítse és tesztelje a leképezési folyamatokat. A Sam bejelentkezik az ADF felhasználói felületére reggel, és lehetővé teszi az adatfolyamatok hibakeresési módját. A hibakeresési munkamenetek alapértelmezett ÉLETTARTAMa 60 perc. A Sam 8 órán keresztül működik a nap folyamán, így a hibakeresési munkamenet soha nem jár le. Ezért a Sam napi díja a következő lesz:

**8 (óra) x 8 (számításra optimalizált magok) x $0,193 = $12,35**

Chris, egy másik adatmérnök is bejelentkezik az ADF-böngésző felhasználói felületére az adatprofilkészítés és az ETL tervezési munkája során. Chris nem működik együtt az ADF-ben, mint a Sam. Chris csak 1 órára kell használnia az adatfolyam-hibakeresőt ugyanabban az időszakban és a fenti Sam-nál is. A hibakeresési használatért felelős Chris inkurzív:

**1 (óra) x 8 (általános célú magok) x $0,274 = $2,19**

## <a name="transform-data-in-blob-store-with-mapping-data-flows"></a>A blob-tárolóban lévő adatátalakítások leképezése adatfolyamatokkal

Ebben a forgatókönyvben a blob-tárolóban lévő adatátalakítást az ADF-leképezési adatfolyamatok óránkénti időpontjában szeretné átalakítani.

A forgatókönyv végrehajtásához létre kell hoznia egy folyamatot a következő elemekkel:

1. Egy adatfolyam-tevékenység az átalakítási logikával.

2. Az Azure Storage-ban tárolt adatok bemeneti adatkészlete.

3. Az Azure Storage-ban tárolt adat kimeneti adatkészlete.

4. Egy ütemezett trigger a folyamat óránkénti végrehajtásához.

| **Műveletek** | **Típusok és egységek** |
| --- | --- |
| Társított szolgáltatás létrehozása | 2 olvasási/írási entitás  |
| Adatkészletek létrehozása | 4 írási/olvasási entitások (2 az adatkészlet létrehozásához, 2 a társított szolgáltatási referenciák esetében) |
| Folyamat létrehozása | 3 olvasási/írási entitás (1 a folyamat létrehozásához, 2 az adatkészlet-hivatkozásokhoz) |
| Folyamat beolvasása | 1 olvasási/írási entitás |
| Folyamat futtatása | 2 tevékenység fut (1 a trigger futtatásához, 1 a tevékenység futtatásához) |
| Adatáramlási feltételezések: végrehajtási idő = 10 perc + 10 perc TTL | 10 16%-os \* általános számítási és TTL-es mag |
| Figyelő folyamat feltételezése: csak 1 Futtatás történt | 2 a figyelő futtatási rekordjainak beolvasása (1 a folyamat futtatásához, 1 a tevékenység futtatásához) |

**Forgatókönyvek teljes díjszabása: $1,4631**

- Data Factory műveletek = **$0,0001**
  - Olvasás/írás = 10 \* 00001 = $0,0001 [1 R/W = $0,50/50000 = 0,00001]
  - Figyelés = 2 \* 000005 = $0,00001 [1 figyelés = $0,25/50000 = 0,000005]
- Folyamat-előkészítési &amp; végrehajtás = **$1,463**
  - Tevékenység-futtatások = 001 \* 2 = 0,002 [1 Futtatás = $1/1000 = 0,001]
  - Adatfolyam-tevékenységek = $1,461 arány 20 percre (10 perc végrehajtási idő + 10 perc TTL). $0.274/óra Azure Integration Runtime 16 maggal általános számítási feladatokkal

## <a name="data-integration-in-azure-data-factory-managed-vnet"></a>Adatintegráció a Azure Data Factory felügyelt VNET
Ebben az esetben törölni kívánja az eredeti fájlokat az Azure Blob Storageon, és az adatok Azure SQL Databaseból az Azure-ba Blob Storage másolhatók. Ezt a végrehajtást kétszer kell elvégeznie a különböző folyamatokon. A két folyamat végrehajtási ideje átfedésben van.
![](media/pricing-concepts/scenario-4.png)A forgatókönyv végrehajtásához két folyamatot kell létrehoznia a következő elemek Scenario4:
  - Egy folyamat tevékenység – törlési tevékenység.
  - Egy másolási tevékenység egy bemeneti adatkészlettel az Azure Blob Storage-ból másolandó adatokhoz.
  - A Azure SQL Database lévő adat kimeneti adatkészlete.
  - Egy ütemezett eseményindító a folyamat végrehajtásához.


| **Műveletek** | **Típusok és egységek** |
| --- | --- |
| Társított szolgáltatás létrehozása | 4 olvasási/írási entitás |
| Adatkészletek létrehozása | 8 olvasási/írási entitás (4 az adatkészlet létrehozásához, 4 a társított szolgáltatási referenciák esetében) |
| Folyamat létrehozása | 6 olvasási/írási entitás (2 a folyamat létrehozásához, 4 az adatkészlet-hivatkozásokhoz) |
| Folyamat beolvasása | 2 olvasási/írási entitás |
| Folyamat futtatása | 6 tevékenység fut (2 a trigger futtatásához, 4 a tevékenység futtatásához) |
| Végrehajtási törlési tevékenység: minden végrehajtási idő = 5 perc. Az első folyamat törlési tevékenységének végrehajtása 10:00 órától UTC és 10:05 között van. A második folyamat törlési tevékenységének végrehajtása 10:02-tól UTC-ig 10:07 AM UTC-re esik.|A felügyelt VNET összesen 7 perces folyamat-végrehajtási művelet. A folyamat tevékenysége legfeljebb 50 egyidejűséget támogat a felügyelt VNET. |
| Adatok másolása feltételezés: minden végrehajtási idő = 10 perc. Az első adatcsatorna másolási végrehajtása 10:06 órától UTC, 10:15 AM UTC. A második folyamat törlési tevékenységének végrehajtása 10:08-tól UTC-ig 10:17 AM UTC-re esik. | 10 * 4 Azure Integration Runtime (alapértelmezett DIU-beállítás = 4) az adatintegrációs egységekkel és a másolási teljesítmény optimalizálásával kapcsolatos további információkért tekintse meg [ezt a cikket.](copy-activity-performance.md) |
| Figyelő folyamat feltételezése: csak 2 Futtatás történt | 6 figyelési futtatási rekordok beolvasva (2 a folyamat futtatásához, 4 a tevékenység futtatásához) |


**Forgatókönyvek teljes díjszabása: $0,45523**

- Data Factory műveletek = $0,00023
  - Olvasás/írás = 20 * 00001 = $0,0002 [1 R/W = $0,50/50000 = 0,00001]
  - Figyelés = 6 * 000005 = $0,00003 [1 figyelés = $0,25/50000 = 0,000005]
- Folyamat-összehangolás & végrehajtás = $0,455
  - Tevékenység-futtatások = 0,001 * 6 = 0,006 [1 Futtatás = $1/1000 = 0,001]
  - Adatáthelyezési tevékenységek = $0,333 (a végrehajtási idő 10 perce arányban. 0,25 USD/óra Azure Integration Runtime)
  - Folyamat aktivitása = $0,116 (a végrehajtási idő 7 perce arányban. $1/óra Azure Integration Runtime)

> [!NOTE]
> Ezek az árak csak példaként szolgálnak.

**Gyakori kérdések**

K: Ha több mint 50 folyamatot kívánok futtatni, akkor ezeket a tevékenységeket egyszerre hajthatja végre?

A: legfeljebb 50 egyidejű folyamatra vonatkozó művelet engedélyezett.  A 51th folyamata tevékenység várólistára kerül, amíg meg nem nyit egy "szabad tárolóhelyet". Ugyanaz, mint a külső tevékenység. Legfeljebb 800 egyidejű külső tevékenység lesz engedélyezett.

## <a name="next-steps"></a>Következő lépések

Most, hogy megértette Azure Data Factory díjszabását, megkezdheti!

- [Adat-előállító létrehozása az Azure Data Factory felhasználói felületén](quickstart-create-data-factory-portal.md)

- [Az Azure Data Factory bemutatása](introduction.md)

- [Visual authoring in Azure Data Factory](author-visually.md)
