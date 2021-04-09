---
title: Azure Data Lake Storage Gen1 Performance tuning – PowerShell
description: Tippek a teljesítmény javításához a Azure PowerShell és a Azure Data Lake Storage Gen1 használatával.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 01/09/2018
ms.author: twooley
ms.openlocfilehash: 4ac2bbb21fd1a987d544a536d0f52628824e0bf4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97723796"
---
# <a name="performance-tuning-guidance-for-using-powershell-with-azure-data-lake-storage-gen1"></a>Teljesítmény-finomhangolási útmutató a PowerShell és a Azure Data Lake Storage Gen1 használatával

Ez a cikk azokat a tulajdonságokat ismerteti, amelyekkel jobb teljesítményt érhet el, miközben a PowerShell használatával együttműködik a Data Lake Storage Gen1okkal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="performance-related-properties"></a>Teljesítménnyel kapcsolatos tulajdonságok

| Tulajdonság            | Alapértelmezett | Leírás |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Ez a paraméter lehetővé teszi a párhuzamos szálak számának megadását az egyes fájlok fel- vagy letöltéséhez. Ez a szám a fájlok számára lefoglalható maximális szálakat jelöli, de előfordulhat, hogy a forgatókönyvtől függően kevesebb szálat kap (például ha egy 1 KB-os fájlt tölt fel, akkor is egy szálat kap, ha 20 szálat kér).  |
| ConcurrentFileCount | 10      | Ez a paraméter kifejezetten a mappák fel- és letöltéséhez kapcsolódik. Ez a paraméter határozza meg az egyidejűleg fel- vagy letölthető fájlok számát. Ez a szám azt jelenti, hogy legfeljebb hány egyidejű fájl tölthető fel vagy tölthető le egyszerre, de előfordulhat, hogy a forgatókönyvtől függően kisebb a párhuzamosság (például ha két fájlt tölt fel, akkor is két egyidejű fájlt tölt fel, akkor is, ha 15-et kér). |

**Példa**

Ez a parancs letölti a fájlokat a Data Lake Storage Gen1ról a felhasználó helyi meghajtójába, és a fájl 20 szálat és 100 egyidejű fájlt használ.

```PowerShell
Export-AzDataLakeStoreItem -AccountName "Data Lake Storage Gen1 account name" `
    -PerFileThreadCount 20 `
    -ConcurrentFileCount 100 `
    -Path /Powershell/100GB `
    -Destination C:\Performance\ `
    -Force `
    -Recurse
```

## <a name="how-to-determine-property-values"></a>Tulajdonságértékek meghatározása

A következő kérdés, amellyel meghatározhatja, hogy milyen értéket kell megadnia a teljesítménnyel kapcsolatos tulajdonságokhoz. Az alábbiakban olvashat némi útmutatást ezzel kapcsolatban.

* **1. lépés: a szálak teljes számának meghatározása** – Kezdje a használandó szálak számának kiszámításával. Általános útmutatóként minden fizikai mag esetében hat szálat kell használni.

    `Total thread count = total physical cores * 6`

    **Példa**

    Tételezzük fel, hogy egy 16 maggal rendelkező D14 VM-en futtatja a PowerShell-parancsokat.

    `Total thread count = 16 cores * 6 = 96 threads`

* **2. lépés: a PerFileThreadCount kiszámítása** – a PerFileThreadCount a fájlok mérete alapján számítjuk ki. A 2,5 GB-nál kisebb fájlok esetében a paramétert nem kell módosítani, mert az alapértelmezett 10 érték elegendő. A 2,5 GB-nál nagyobb fájlok esetében 10 szálat kell használni az első 2,5 GB alapértékként, majd 1 szálat kell hozzáadnia minden további 256-MB-os mérethez. Ha olyan mappát másol, amelyben nagyon eltérő méretű fájlok találhatók, érdemes hasonló fájlméret alapján csoportosítani a fájlokat. Az eltérő fájlméretek az optimálisnál kisebb teljesítménnyel járhatnak. Ha nem lehetséges a hasonló fájlméret alapján történő csoportosítás, akkor a PerFileThreadCount értékét a legnagyobb fájlméret alapján kell beállítani.

    `PerFileThreadCount = 10 threads for the first 2.5 GB + 1 thread for each additional 256 MB increase in file size`

    **Példa**

    Feltételezve, hogy a 100-es fájlok száma 1 GB és 10 GB között van, a 10 GB-ot használjuk a legnagyobb fájlméretként az egyenlethez, ami az alábbihoz hasonló lesz.

    `PerFileThreadCount = 10 + ((10 GB - 2.5 GB) / 256 MB) = 40 threads`

* **3. lépés: a ConcurrentFilecount kiszámítása** – a ConcurrentFilecount kiszámításához használja a szálak számát és a PerFileThreadCount a következő egyenlet alapján:

    `Total thread count = PerFileThreadCount * ConcurrentFileCount`

    **Példa**

    Az eddig használt példaértékek alapján

    `96 = 40 * ConcurrentFileCount`

    Tehát a **ConcurrentFileCount** értéke **2,4**, amelyet kerekíthetünk **2**-re.

## <a name="further-tuning"></a>További hangolás

Elképzelhető, hogy további hangolásra lesz szüksége, mert különböző fájlméretekkel kell dolgoznia. Az előző számítás jól működik, ha az összes vagy a legtöbb fájl nagyobb, és közelebb van a 10 GB-os tartományhoz. Ha ehelyett sok különböző, nagyon eltérő méretű fájllal rendelkezik, akkor csökkentheti a PerFileThreadCount értékét. A PerFileThreadCount értékének csökkentésével, növelhetjük a ConcurrentFileCount értékét. Tehát ha feltételezzük, hogy a legtöbb fájl mérete kisebb az 5 GB-os tartományon, akkor megismételjük a számítást:

`PerFileThreadCount = 10 + ((5 GB - 2.5 GB) / 256 MB) = 20`

Így a **ConcurrentFileCount** 96/20 lesz, amely 4,8, **4**-re kerekítve.

A beállítások hangolását a **PerFileThreadCount** értékének növelésével vagy csökkentésével folytathatja a fájlméretek eloszlásától függően.

### <a name="limitation"></a>Korlátozás

* **A fájlok száma kisebb, mint a ConcurrentFileCount értéke**: Ha a feltölteni kívánt fájlok száma kisebb a **ConcurrentFileCount** kiszámított értékének, akkor csökkentse a **ConcurrentFileCount** értékét úgy, hogy az egyenlő legyen a fájlok számával. A fennmaradó szálakat a **PerFileThreadCount** értékének a növelésére használhatja.

* **Túl sok szál**: Ha túlságosan megnöveli a szálszámot a fürt méretének növelése nélkül, az rosszabb teljesítménnyel járhat. Versengési problémák adódhatnak, ha kontextusváltás történik a CPU-n.

* **Elégtelen egyidejűség**: Ha az egyidejűség nem elégséges, lehet, hogy túl kicsi a fürt. Növelheti a fürtben lévő csomópontok számát, ami nagyobb párhuzamosságot biztosít.

* **Szabályozási hibák**: Elképzelhető, hogy szabályozási hibákat tapasztal, ha az egyidejűség túl magas. Ha szabályozás hibák merülnek fel, csökkentse az egyidejűséget vagy lépjen kapcsolatba velünk.

## <a name="next-steps"></a>Következő lépések

* [Azure Data Lake Storage Gen1 használata big data követelményekhez](data-lake-store-data-scenarios.md) 
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)
* [Azure Data Lake Analytics használata a Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

