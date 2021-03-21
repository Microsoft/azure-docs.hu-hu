---
title: Prémium szintű Azure Data Lake Storage | Microsoft Docs
description: A prémium szintű teljesítményszint használata Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.custom: references_regions
ms.date: 10/30/2020
ms.author: normesta
ms.openlocfilehash: be440407fb6f4d9715ba80c584af023a5f662394
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93324227"
---
# <a name="premium-tier-for-azure-data-lake-storage"></a>Prémium szintű Azure Data Lake Storage

A Azure Data Lake Storage Gen2 mostantól támogatja a [prémium szintű teljesítményt](storage-blob-performance-tiers.md#premium-performance). A prémium szintű teljesítményszint olyan big data elemzési alkalmazások és munkaterhelések esetén ideális, amelyek alacsony konzisztens késést és nagy számú tranzakciót igényelnek.

## <a name="workloads-that-can-benefit-from-the-premium-performance-tier"></a>A prémium szintű teljesítményszint előnyeit kihasználó munkaterhelések

A számítási feladatok közé tartoznak például az interaktív számítási feladatok, a IoT, a streaming Analytics, a mesterséges intelligencia és a gépi tanulás. 

**Interaktív számítási feladatok** 

Ezeknek a számítási feladatoknak azonnali frissítésre és felhasználói visszajelzésre van szükségük, például az e-kereskedelmi és a leképezési alkalmazások, az interaktív videós alkalmazások stb. Egy e-kereskedelmi alkalmazásban például a ritkábban megtekintett elemek valószínűleg nem lesznek gyorsítótárazva. Ezeket azonban azonnal meg kell jeleníteni az ügyfél számára igény szerint. Egy másik példa, az adatszakértők, az elemzők és a fejlesztők a prémium szintű teljesítményt használó fiókban tárolt adatokon futó lekérdezések futtatásával akár gyorsabban is elérhetik az időérzékeny információkat. 

**IoT/streaming Analytics** 

Egy IoT-forgatókönyvben sok kisebb írási műveletet lehet leküldeni a felhőbe másodpercenként. Nagy mennyiségű adatot lehet bevenni, elemezni az elemzési célokat, majd szinte azonnal törölni. A prémium szintű teljesítmény magas betöltési képességeinek köszönhetően az ilyen típusú számítási feladatok hatékonyan használhatók. 

**Mesterséges intelligencia/gépi tanulás (AI/ML)** 

Az AI/ML különféle adattípusok, például vizualizációk, beszédek és szövegek felhasználását és feldolgozását tárgyalja. Ez a nagy teljesítményű számítási feladatok olyan nagy mennyiségű adatot biztosítanak, amely gyors reagálást és hatékony betöltési időt igényel az adatok elemzéséhez. 

## <a name="cost-effectiveness"></a>Költséghatékonyság

A prémium szintű teljesítményszint magasabb tárolási kapacitással rendelkezik, azonban alacsonyabb tranzakciós költségeket biztosít a standard szintű teljesítményhez képest. Ha alkalmazásai és munkaterhelései nagy számú tranzakciót hajtanak végre, a prémium szintű teljesítményszint költséghatékony lehet.

Az alábbi táblázat a Azure Data Lake Storage prémium szintjének költséghatékonyságát mutatja be. Minden oszlop a hónapokbeli tranzakciók számát jelöli.  Minden sor az olvasási tranzakciós tranzakciók százalékos arányát jelöli. A tábla minden cellája az olvasási tranzakciók százalékos arányának és a végrehajtott tranzakciók számának százalékos arányát jeleníti meg. 

Tegyük fel például, hogy a fiókja az USA 2. keleti régiójában található, a fiókhoz tartozó tranzakciók száma meghaladja a 90, és a tranzakciók 70%-a olvasási tranzakció, a prémium szintű teljesítményszint költséghatékonyabb.

> [!div class="mx-imgBorder"]
> ![a rendszerkép ide kerül](./media/premium-tier-for-data-lake-storage/premium-performance-data-lake-storage-cost-analysis-table.png)

> [!NOTE] 
> Ha a költséghatékonyságot a másodpercenkénti tranzakciók másodpercenkénti száma alapján szeretné kiértékelni, akkor használhatja a tábla alján megjelenő oszlopfejléceket.

A díjszabással kapcsolatos további információkért tekintse meg a [Azure Data Lake Storage Gen2 díjszabását](https://azure.microsoft.com/pricing/details/storage/data-lake/) ismertető oldalt.

## <a name="feature-availability"></a>Szolgáltatások rendelkezésre állása 

Előfordulhat, hogy egyes blob Storage-funkciók nem érhetők el, vagy csak részben támogatják a prémium szintű teljesítményt. A teljes listát lásd: [blob Storage-funkciók érhetők el Azure Data Lake Storage Gen2ban](data-lake-storage-supported-blob-storage-features.md). Ezt követően tekintse át az [ismert problémák](data-lake-storage-known-issues.md) listáját, hogy felmérje az esetleges hiányosságokat a funkcionalitásban.

## <a name="enabling-the-premium-performance-tier"></a>A prémium szintű teljesítményszint engedélyezése 

A Azure Data Lake Storage prémium szintjének létrehozásához hozzon létre egy BlockBlobStorage-fiókot, amelyen **engedélyezve van** a **hierarchikus névtér** beállítás. A teljes körű útmutatásért lásd: [BlockBlobStorage fiók fiók létrehozása](storage-blob-create-account-block-blob.md) .

A fiók létrehozásakor győződjön meg arról, hogy a **prémium** szintű teljesítmény és a **BlockBlobStorage** fiók van kiválasztva.

> [!div class="mx-imgBorder"]
> ![Blockblobstorageacount létrehozása](./media/premium-tier-for-data-lake-storage/create-block-blob-storage-account.png)

Engedélyezze a **hierarchikus névtér** beállítást a **Storage-fiók létrehozása** lap **speciális** lapján. A fiók létrehozásakor engedélyeznie kell ezt a beállítást. Ezt követően nem engedélyezheti.

A következő képen a **Storage-fiók létrehozása** lapon látható ez a beállítás.

> [!div class="mx-imgBorder"]
> ![Hierarchikus névtér beállítása](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

## <a name="regional-availability"></a>Regionális elérhetőség

A Azure Data Lake Storage prémium szintje a következő régiókban érhető el.

|Region|Redundancia|
|--|--|
|USA keleti régiója|LRS, ZRS|
|USA 2. keleti régiója|LRS, ZRS|
|Az USA középső régiója|LRS|
|USA nyugati régiója|LRS|
|USA 2. nyugati régiója|LRS, ZRS|
|USA nyugati középső régiója|LRS|
|USA déli középső régiója|LRS|
|Közép-Kanada|LRS|
|Kelet-Kanada|LRS|
|Észak-Európa|LRS, ZRS|
|Nyugat-Európa|LRS, ZRS|
|Az Egyesült Királyság déli régiója|LRS|
|Az Egyesült Királyság nyugati régiója|LRS|
|Közép-Franciaország|LRS|
|Kelet-Ázsia|LRS|
|Dél-Korea középső régiója|LRS|
|Dél-Korea déli régiója|LRS|
|Közép-India|LRS|
|Nyugat-India|LRS|
|Észak-Egyesült Arab|LRS|
|Kelet-Japán|LRS|
|Nyugat-Japán|LRS|
|Délkelet-Ázsia|LRS, ZRS|
|Kelet-Ausztrália|LRS, ZRS|
|Délkelet-Ausztrália|LRS|
|Dél-Brazília|LRS|

## <a name="next-steps"></a>Következő lépések

A prémium szintű Azure Data Lake Storage a kedvenc analitikai szolgáltatásával, például a Azure Databricks, az Azure HDInsight és az Azure szinapszis Analytics használatával. 

- [Oktatóanyag: Azure Data Lake Storage Gen2, Azure Databricks & Spark](data-lake-storage-use-databricks-spark.md) 
- [Azure Data Lake Storage Gen2 használata az Azure HDInsight-fürtökkel](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) A HDInsight jelenleg olyan fiókot támogat, amely a prémium szintű teljesítményt használja, valamint egy olyan HBase-fürtöt, amelynél engedélyezve van a gyorsított írás.
- [Rövid útmutató: szinapszis-munkaterület létrehozása](../../synapse-analytics/quickstart-create-workspace.md)

