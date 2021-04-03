---
title: Azure Data Lake Storage lekérdezés gyorsulása
description: A lekérdezési gyorsítás lehetővé teszi az alkalmazások és az elemzési keretrendszerek számára, hogy a feldolgozási művelethez szükséges adatok beolvasásával jelentősen optimalizálják az adatfeldolgozást.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 09/09/2020
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: dc1d217dba64c36aa219abbd4d2220a494347689
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95912756"
---
# <a name="azure-data-lake-storage-query-acceleration"></a>Azure Data Lake Storage lekérdezés gyorsulása

A lekérdezési gyorsítás lehetővé teszi, hogy az alkalmazások és az elemzési keretrendszerek jelentősen optimalizálják az adatfeldolgozást azáltal, hogy csak az adott művelet végrehajtásához szükséges adatok beolvasását végzik. Ez csökkenti a tárolt információk kritikus fontosságú elemzéséhez szükséges időt és feldolgozási teljesítményt.

## <a name="overview"></a>Áttekintés

A lekérdezési gyorsítás olyan szűrési *predikátumokat* és *oszlop-kivetítéseket* fogad el, amelyek lehetővé teszik az alkalmazások számára a sorok és oszlopok szűrését az adatok lemezről történő beolvasása során. Csak a predikátum feltételeit teljesítő adat kerül át a hálózaton keresztül az alkalmazásba. Ez csökkenti a hálózati késést és a számítási költségeket.  

Az SQL segítségével megadhatja a sorcsoport-predikátumokat és az oszlopok kivetítéseit egy lekérdezési gyorsítási kérelemben. Egy kérelem csak egy fájlt dolgoz fel. Ezért az SQL speciális, például összekapcsolási és csoportosítási funkciói nem támogatottak. A lekérdezési gyorsítás támogatja a CSV-és JSON-formátumú adatokat bemenetként az egyes kérésekhez.

A lekérdezési gyorsítási funkció nem korlátozódik Data Lake Storagera (olyan tárolási fiókokra, amelyeken engedélyezve van a hierarchikus névtér). A lekérdezési gyorsítás teljesen kompatibilis a Storage-fiókok azon Blobokkal, amelyeken **nincs** engedélyezve a hierarchikus névtér. Ez azt jelenti, hogy a hálózati késés és a számítási költségek ugyanezt a csökkentést érhetik el, ha olyan adatok feldolgozását végzi, amelyeket blobként tárolt a Storage-fiókokban.

Az ügyfélalkalmazások lekérdezési gyorsításának használatáról a következő témakörben talál példát: az [Adatszűrés Azure Data Lake Storage lekérdezési gyorsítás használatával](data-lake-storage-query-acceleration-how-to.md).

## <a name="data-flow"></a>Adatfolyam

Az alábbi ábra azt szemlélteti, hogy egy átlagos alkalmazás hogyan használja a lekérdezési gyorsítást az adatfeldolgozáshoz.

> [!div class="mx-imgBorder"]
> ![A lekérdezési gyorsítás áttekintése](./media/data-lake-storage-query-acceleration/query-acceleration.png)

1. Az ügyfélalkalmazás predikátumok és oszlopok kivetítések megadásával kéri le a fájlokat.

2. A lekérdezési gyorsítás elemzi a megadott SQL-lekérdezést, és elosztja az adatelemzési és-szűrési munkát.

3. A processzorok beolvasják az adatait a lemezről, a megfelelő formátum használatával elemzik az adatok elemzését, majd a megadott predikátumok és oszlopok kivetítések alkalmazásával szűrik az adatszűrést.

4. A lekérdezési gyorsítás ötvözi a válasz szegmenseit, és visszaküldi az ügyfél-alkalmazásnak.

5. Az ügyfélalkalmazás fogadja és elemzi a továbbított választ. Az alkalmazásnak nem kell további adatszűrést végeznie, és közvetlenül is alkalmazhatja a kívánt számítást vagy átalakítást.

## <a name="better-performance-at-a-lower-cost"></a>Jobb teljesítmény alacsonyabb költséggel

A lekérdezési gyorsítás optimalizálja a teljesítményt azáltal, hogy csökkenti az alkalmazás által átvitt és feldolgozott adatmennyiséget.

Egy összesített érték kiszámításához az alkalmazások általában lekérik az **összes** adatokat egy fájlból, majd helyileg dolgozzák fel és szűrhetik az adatokat. Az elemzési számítási feladatokhoz tartozó bemeneti/kimeneti minták elemzése azt mutatja, hogy az alkalmazások általában csak 20%-át igénylik az általuk beolvasott adatokat az adott számítás elvégzéséhez. Ez a statisztika akkor is igaz, ha olyan technikákat alkalmaz, mint például a [partíciók metszése](../../hdinsight/hdinsight-hadoop-optimize-hive-query.md#hive-partitioning). Ez azt jelenti, hogy az ilyen adatmennyiség 80%-a feleslegesen a hálózaton keresztül történik, és az alkalmazások elemzik és szűrik azokat. Ez a minta, amely lényegében a szükségtelen adatok eltávolítására lett kialakítva, jelentős számítási költségekkel jár.  

Annak ellenére, hogy az Azure egy piacvezető hálózatot is tartalmaz, az átviteli sebesség és a késés tekintetében, az adatok felesleges átvitele a hálózaton keresztül továbbra is költséges az alkalmazások teljesítményére. Ha kiszűri a nemkívánatos adattárolási kérést a tárolási kérelem során, a lekérdezés gyorsulása kiküszöböli ezt a költségeket.

Emellett a szükségtelen adatelemzéshez és szűréshez szükséges CPU-terheléshez az alkalmazásnak nagyobb számú és nagyobb méretű virtuális gépet kell kiépíteni a munkája elvégzéséhez. Ha áthelyezi ezt a számítási terhelést a gyorsításra, az alkalmazások jelentős költségmegtakarítást tudnak megvalósítani.

## <a name="applications-that-can-benefit-from-query-acceleration"></a>A lekérdezési gyorsítás előnyeit kihasználó alkalmazások

A lekérdezési gyorsítás az elosztott elemzési keretrendszerek és az adatfeldolgozási alkalmazások számára készült. 

Az elosztott elemzési keretrendszerek, mint például a Apache Spark és a Apache Hive, a keretrendszerben tartalmazzák a tároló absztrakciós rétegét. Ezek a motorok olyan lekérdezés-Optimalizálók is, amelyek a felhasználói lekérdezések optimális lekérdezési tervének meghatározásakor beépíthetik a mögöttes I/O szolgáltatás funkcióinak ismereteit. Ezek a keretrendszerek kezdik a lekérdezési gyorsítás integrálását. Ennek eredményeképpen ezek a keretrendszerek jobb lekérdezési késést és alacsonyabb teljes tulajdonlási díjat látnak, anélkül, hogy módosítani kellene a lekérdezéseket. 

A lekérdezési gyorsítást adatfeldolgozási alkalmazásokhoz is tervezték. Az ilyen típusú alkalmazások általában nagy léptékű adatátalakításokat végeznek, amelyek nem vezetnek közvetlenül az elemzési elemzésekhez, így nem mindig használják a létrehozott elosztott elemzési keretrendszereket. Ezek az alkalmazások gyakran több közvetlen kapcsolattal rendelkeznek a mögöttes tárolási szolgáltatással, így azok közvetlenül olyan funkciókból részesülhetnek, mint a lekérdezési gyorsítás. 

Az alkalmazások a lekérdezési gyorsítás integrálásának példáját lásd: az [Adatszűrés Azure Data Lake Storage lekérdezési gyorsítás használatával](data-lake-storage-query-acceleration-how-to.md).

## <a name="pricing"></a>Díjszabás

A Azure Data Lake Storage szolgáltatásban megnövelt számítási terhelés miatt a lekérdezési gyorsítás használatának díjszabási modellje eltér a normál Azure Data Lake Storage tranzakciós modelltől. A lekérdezési gyorsítás díja a beolvasott adatok mennyiségének, valamint a hívónak visszaadott adatok mennyiségének a költségei. További információ: [Azure Data Lake Storage Gen2 díjszabása](https://azure.microsoft.com/pricing/details/storage/data-lake/).

A számlázási modellre való váltás ellenére a lekérdezési gyorsítás díjszabási modellje úgy van kialakítva, hogy csökkentse a számítási feladatok teljes tulajdonlási költségét, a jóval drágább virtuális gépek költségeinek csökkentése miatt.

## <a name="next-steps"></a>Következő lépések

- [Az Adatszűrés Azure Data Lake Storage lekérdezési gyorsítás használatával](data-lake-storage-query-acceleration-how-to.md)
- [A lekérdezés gyorsításának SQL nyelvi referenciája](query-acceleration-sql-reference.md)