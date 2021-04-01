---
title: Adatfolyamok az Azure SQL Edge-ben
description: Ismerje meg az Azure SQL Edge adatátviteli szolgáltatását.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: f63ab040e750c0c642c9656a5482529b926e9295
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392112"
---
# <a name="data-streaming-in-azure-sql-edge"></a>Adatfolyamok az Azure SQL Edge-ben

Az Azure SQL Edge a T-SQL streaming nevű adatátviteli képességek natív implementációját biztosítja. Valós idejű adatfolyam-továbbítást, elemzést és eseményt biztosít, így egyszerre több forrásból is elemezheti és feldolgozhatja a gyors adatfolyam-továbbítási adatok nagy mennyiségét. A T-SQL streaming egy olyan nagy teljesítményű streaming motor használatával készült, amely a Microsoft Azure [Azure stream Analytics](../stream-analytics/stream-analytics-introduction.md) . A szolgáltatás a szélén futó Azure Stream Analytics által kínált hasonló képességeket támogatja.

A Stream Analyticshoz hasonlóan a T-SQL streaming felismeri a mintákat és a kapcsolatokat számos IoT bemeneti forrásból, például eszközökből, érzékelőkből és alkalmazásokból kinyert információk alapján. Ezeket a mintákat használhatja a műveletek elindításához és a munkafolyamatok kezdeményezéséhez. Létrehozhat például riasztásokat, adatokat adhat hozzá egy jelentéskészítési vagy vizualizációs megoldáshoz, vagy tárolhatja az adatokat későbbi használatra. 

A T-SQL streaming segítségével:

* Valós idejű telemetria streamek elemzése a IoT-eszközökről.
* Az autonóm és az illesztőprogram nélküli járművekből generált adatok valós idejű elemzési módszereit használhatja.
* Használjon távoli monitorozást és prediktív karbantartást a nagy értékű ipari vagy gyártási eszközökhöz.
* A IoT-érzékelő beolvasása a mezőgazdaságban vagy az energia-farmban anomáliák észlelésével és a minták felismerésével.

## <a name="how-does-t-sql-streaming-work"></a>Hogyan működik a T-SQL streaming?

A T-SQL streaming pontosan ugyanolyan módon működik, mint a [Azure stream Analytics](../stream-analytics/stream-analytics-introduction.md#how-does-stream-analytics-work). Például a folyamatos átviteli *feladatok* koncepcióját használja a valós idejű adatfolyamok feldolgozásához. 

A stream Analytics-feladatok a következőkből állnak:

- **Stream bemenete**: Ez határozza meg az adatforrással létesített kapcsolatokat, hogy beolvassa az adatfolyamot a rendszerből. Az Azure SQL Edge jelenleg a következő stream típusú bemeneti típusokat támogatja:
    - Peremhálózati hub
    - Kafka (a Kafka-bemenetek támogatása jelenleg csak az Azure SQL Edge Intel/AMD64 verziójában érhető el.)

- **Stream kimenete**: Ez határozza meg az adatforráshoz való kapcsolódást az adatfolyamnak a szolgáltatásba való írásához. Az Azure SQL Edge jelenleg a következő stream-kimeneti típusokat támogatja
    - Peremhálózati hub
    - SQL (az SQL-kimenet lehet az Azure SQL Edge példányán belüli helyi adatbázis, illetve távoli SQL Server vagy Azure SQL Database.) 

- **Stream-lekérdezés**: Ez határozza meg a bemeneti adatfolyamra alkalmazni kívánt átalakítást, összesítéseket, szűrést, rendezést és illesztéseket, mielőtt az adatfolyam kimenetére íródik. Az adatfolyam-lekérdezés ugyanarra a lekérdezési nyelvre épül, mint amelyet a Stream Analytics használ. További információ: [stream Analytics lekérdezési nyelv](/stream-analytics-query/stream-analytics-query-language-reference).

> [!IMPORTANT]
> A T-SQL streaming a Stream Analyticstól eltérően jelenleg nem támogatja a keresések [és az UDF és a uda adatfolyam-](../stream-analytics/streaming-technologies.md#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c) [feladatokban](../stream-analytics/stream-analytics-use-reference-data.md) való használatát.

> [!NOTE]
> A T-SQL streaming csak a Stream Analytics által támogatott nyelvi felület részhalmazát támogatja. További információ: [stream Analytics lekérdezési nyelv](/stream-analytics-query/stream-analytics-query-language-reference).

## <a name="limitations-and-restrictions"></a>Korlátozások és korlátozások

A következő korlátozások és korlátozások érvényesek a T-SQL streamingre. 

- Egy adott időpontban csak egy folyamatos átviteli feladatok lehetnek aktívak. A már futó feladatokat le kell állítani egy másik feladat elindítása előtt.
- A folyamatos átviteli feladatok végrehajtása egyetlen szálból áll. Ha a folyamatos átviteli feladatok több lekérdezést is tartalmaznak, a rendszer a lekérdezéseket soros sorrendben értékeli ki.
- Ha leállított egy folyamatos átviteli feladatot az Azure SQL Edge-ben, a következő folyamatos átviteli feladatok elindításához némi késés fordulhat elő. Ez a késleltetés azért van bevezetve, mert az alapul szolgáló folyamatos átviteli folyamatot le kell állítani a leállítási kérelemre adott válaszban, majd újra kell indítani az indítási feladatra vonatkozó kérelemre. 
- A T-SQL streaming upto 32 partíciót egy Kafka-adatfolyamhoz. A magasabb partíciók számának konfigurálására tett kísérletek hibát eredményeznek. 

## <a name="next-steps"></a>Következő lépések

- [Stream Analytics-feladatok létrehozása az Azure SQL Edge-ben ](create-stream-analytics-job.md)
- [A stream-feladatokhoz kapcsolódó metaadatok megtekintése az Azure SQL Edge-ben ](streaming-catalog-views.md)
- [Külső stream létrehozása](create-external-stream-transact-sql.md)