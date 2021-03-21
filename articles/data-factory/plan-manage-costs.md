---
title: A Azure Data Factory költségeinek megtervezése és kezelése
description: Ez a cikk a Azure Data Factory költségeinek megtervezését és kezelését ismerteti.
author: shirleywangmsft
ms.author: shwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/14/2020
ms.openlocfilehash: d89275acdb8e13a792ea3166d874fabf911bb686
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100372892"
---
# <a name="plan-and-manage-costs-for-azure-data-factory"></a>A Azure Data Factory költségeinek megtervezése és kezelése

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Azure Data Factory a Felhőbeli méretezéshez készült, kiszolgáló nélküli és rugalmas adatintegrációs szolgáltatás.  Ez azt jelenti, hogy nem rögzített méretű számításra van szükség a maximális terhelés megtervezéséhez; Ehelyett megadhatja, hogy mennyi erőforrást kell igény szerint lefoglalni a művelethez, ami lehetővé teszi, hogy az ETL-folyamatokat sokkal méretezhető módon tervezze meg. Az ADF emellett egy fogyasztáson alapuló csomagra is kiszámlázható, ami azt jelenti, hogy csak a ténylegesen használt funkciókért kell fizetnie.

Ez a cikk a Azure Data Factory költségeinek megtervezését és kezelését ismerteti.

*   Először is, az ETL projekt elején elvégezheti a koncepció igazolását, és a költségek kiszámításához használja a folyamaton belüli és a díjszabási számológép kombinációját.
*   Miután üzembe helyezte a folyamatokat az éles környezetbe, a Cost Management szolgáltatással állíthatja be a költségvetéseket, és figyelheti a költségeket. Emellett áttekintheti az előre jelzett költségeket, és azonosíthatja a kiadási trendeket.
*   Emellett megtekintheti a folyamaton belüli és a tevékenységekre vonatkozó használati adatokat is, amelyekkel megtudhatja, hogy mely folyamatok és milyen tevékenységek costliest, és azonosítsa a jelöltek számát a költségcsökkentés érdekében.

## <a name="estimate-costs-using-pipeline-and-activity-run-consumption-and-pricing-calculator"></a>A költségek becslése a folyamat és a tevékenység futása és árképzési kalkulátor használatával

Az [ADF árképzési számológép](https://azure.microsoft.com/pricing/calculator/?service=data-factory) használatával megbecsülheti az ETL-számítási feladatok Azure Data Factory-ben való futtatásának költségeit.  A számológép használatához olyan adatokat kell beírnia, mint például a tevékenységek száma, az adatintegrációs egység órája, az adatfolyamhoz használt számítási típus, a mag darabszáma, a példányok száma, a végrehajtás időtartama stb.

A díjszabási számológép egyik leggyakrabban feltett kérdése, hogy milyen értékeket kell használni bemenetként.  A próba-koncepciós fázis során próbaverziós futtatásokat végezhet a különböző ADF-mérőszámok fogyasztásának megismeréséhez minta-adatkészletek használatával.  Ezután a minta adatkészlet fogyasztása alapján kitervezheti a teljes adatkészlet és a operacionalizálási-ütemterv felhasználását.

> [!NOTE]
> Az alábbi példákban használt árak feltételezettek, és nem jelentenek tényleges díjszabást.

Tegyük fel például, hogy naponta 1 TB-nyi adatmennyiséget kell áthelyeznie az AWS S3-ból Azure Data Lake Gen2.  Az adatfeldolgozási teljesítmény mérésére, valamint a megfelelő számlázási használat megismeréséhez a 100 GB-nyi adat áthelyezését a POC használatával végezheti el.

Itt látható a másolási tevékenység futtatási részletei (a tényleges futásteljesítmény az adott adatkészlet, a hálózati sebesség, a kimenő korlátok az S3-fiókra, a ADLS Gen2 korlátozásai és egyéb tényezők) alapján változhat.

![S3 másolási Futtatás](media/plan-manage-costs/s3-copy-run-details.png)

A [folyamat futási szintjének felhasználásának figyelésével](#monitor-consumption-at-pipeline-run-level)megtekintheti a megfelelő adatátviteli mérőszámok felhasználási mennyiségét:

![S3 másolási folyamat felhasználása](media/plan-manage-costs/s3-copy-pipeline-consumption.png)

Ezért a teljes hónapra a napi 1 TB-nyi DIU szükséges órák teljes száma:

1,2667 (DIU óra) * (1 TB/100 GB) * 30 (nap egy hónapban) = 380 DIU óra

A havi számla becsléséhez mostantól a 30 tevékenység-és a 380-es DIU is csatlakoztathatók az ADF árképzési kalkulátorba:

![S3 másolás árképzési kalkulátor](media/plan-manage-costs/s3-copy-pricing-calculator.png)

## <a name="use-budgets-and-cost-alerts"></a>Költségvetések és költségriasztások használata

A költségek kezeléséhez [költségvetéseket](../cost-management-billing/costs/tutorial-acm-create-budgets.md) és riasztásokat hozhat létre, amelyek automatikusan figyelmeztetik az érdekelt feleket a rendellenes kiadásokról és a túlköltekezési kockázatokról.  A riasztások a költségvetés és a költségek küszöbértékei alapján működnek.  Amikor létrehoz egy költségvetést, megteheti az előfizetés szintjén vagy alacsonyabb részletességgel, ha további szűrőket ad hozzá, például az erőforrás-azonosítót és a mérőszám nevét.  Azonban nem hozhat létre költségvetést az egyes folyamatokhoz a gyáron belül.

## <a name="monitor-costs-at-factory-level"></a>A költségek figyelése a gyári szinten

A Azure Data Factory használatának megkezdése után megtekintheti a Azure Portal [Cost Analysis](../cost-management-billing/costs/quick-acm-cost-analysis.md) ablaktáblájában felmerülő költségeket.

1. A [Cost Analysis](../cost-management-billing/costs/quick-acm-cost-analysis.md)megtekintéséhez nyissa meg a **Cost Management + számlázás** ablakot, válassza ki a menüből a **Cost Management** elemet, majd válassza a **nyitott Cost-elemzés** lehetőséget.
2. Az alapértelmezett nézet az aktuális hónap összesített költségeit jeleníti meg.  Átválthat egy másik időtartományra, és más részletességgel is, például naponta vagy havonta.
3. Egy szolgáltatás, például a Azure Data Factory költségeinek szűkítéséhez válassza a **szűrő hozzáadása** , majd a **szolgáltatásnév** lehetőséget.  Ezután válassza ki az Azure-beli **adatok Factory v2** elemet a listából.
4. További szűrőket is hozzáadhat, amelyekkel elemezheti az adott gyári példány költségeit és az adott ADF-fogyasztásmérő részletességét.

   ![Költségelemzés](media/plan-manage-costs/cost-analysis.png)

## <a name="monitor-consumption-at-pipeline-run-level"></a>Használat figyelése a folyamat futási szintjén

Attól függően, hogy milyen típusú tevékenységeket végez a folyamatában, milyen mennyiségű adatátvitelt és átalakítást hajt végre, az átalakítás összetettsége pedig a Azure Data Factory különböző számlázási mérőszámait fogja kimutatni.

A Azure Data Factory felhasználói élményben megtekintheti az egyes adatcsatornákhoz tartozó különböző mérőszámok felhasználásának mennyiségét. A figyelési élmény megnyitásához válassza a **figyelő & kezelés** csempét a [Azure Portal](https://portal.azure.com/)adatgyár paneljén. Ha már szerepel az ADF UX-ben, kattintson a bal oldali oldalsávon látható **figyelés** ikonra. Az alapértelmezett figyelési nézet a folyamat-futtatások listáját tartalmazza.

Ha a folyamat neve melletti **fogyasztás** gombra kattint, megjelenik egy előugró ablak, amely a folyamaton belüli összes tevékenység összesített számát mutatja.

![Folyamat-futtatási felhasználás](media/plan-manage-costs/pipeline-run-consumption.png)

![A folyamat felhasználásának részletei](media/plan-manage-costs/pipeline-consumption-details.png)

A folyamat-futtatási használat nézet az egyes ADF-mérőszámok mennyiségét mutatja az adott folyamat futtatásához, de nem jeleníti meg a tényleges díjat, mert a számlán felhasznált összeg a kapott Azure-fiók típusától és a használt pénznem típusától függ.  A támogatott fióktípusok teljes listáját lásd: [A Cost Management adatainak értelmezése](../cost-management-billing/costs/understand-cost-mgt-data.md).

## <a name="monitor-consumption-at-activity-run-level"></a>A felhasználás figyelése tevékenység-futtatási szinten
Miután megértette az összesített felhasználást a folyamat futási szintjén, vannak olyan forgatókönyvek, ahol további részletezést és azonosítást kell végeznie, amely a folyamaton belül a legdrágább tevékenység.

A tevékenység futtatási szintjének megjelenítéséhez nyissa meg a **& monitor** felhasználói felületét. A **figyelés** lapon a folyamat-futtatások listájának megjelenítéséhez kattintson a **folyamat neve** hivatkozásra a folyamat futtatása során futó tevékenységek listájának eléréséhez.  Kattintson a tevékenység neve melletti **kimenet** gombra, és keresse meg a **billableDuration** tulajdonságot a JSON-kimenetben:

Itt látható egy példa egy másolási tevékenység futtatására:

![Kimenet másolása](media/plan-manage-costs/copy-output.png)

Itt látható egy példa a leképezési adatfolyam tevékenységének futtatására:

![Adatfolyam kimenete](media/plan-manage-costs/dataflow-output.png)

## <a name="next-steps"></a>Következő lépések

Az alábbi cikkekből többet tudhat meg a díjszabás működéséről Azure Data Factoryban:

- [Azure Data Factory díjszabási oldala](https://azure.microsoft.com/pricing/details/data-factory/ssis/)
- [A Azure Data Factory ismertetése példákkal](./pricing-concepts.md)
- [Azure Data Factory árképzési kalkulátor](https://azure.microsoft.com/pricing/calculator/?service=data-factory)