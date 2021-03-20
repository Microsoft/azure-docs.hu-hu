---
title: Mi az Azure SQL Edge?
description: Tudnivalók az Azure SQL Edge-ről
keywords: az SQL Edge bemutatása, mi az SQL Edge, az SQL Edge áttekintése
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a74e12a5e9bdd2dfdbc6ac07b66798e517f6f426
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93395070"
---
# <a name="what-is-azure-sql-edge"></a>Mi az Azure SQL Edge?

Az Azure SQL Edge egy optimalizált, a IoT és a IoT Edge üzemelő példányokhoz kapcsolódó adatbázis-motor. Lehetőséget biztosít nagy teljesítményű adattárolási és feldolgozási réteg létrehozására a IoT alkalmazások és megoldások számára. Az Azure SQL Edge lehetővé teszi a kapcsolatok és a nem rokonok, például a JSON, a Graph és az idősoros adatsorozatok továbbítását, feldolgozását és elemzését, ami a megfelelő választás számos modern IoT alkalmazáshoz.

Az Azure SQL Edge az [SQL Server adatbázismotor](/sql/sql-server/sql-server-technical-documentation)legújabb verziójára épül, amely piacvezető teljesítményt, biztonsági és lekérdezés-feldolgozási képességeket biztosít. Mivel az Azure SQL Edge ugyanarra a motorra épül, mint a [SQL Server](/sql/sql-server/sql-server-technical-documentation) és az [Azure SQL](../azure-sql/index.yml), ugyanazt a Transact-SQL (T-SQL) programozási felületet biztosítja, amely megkönnyíti és gyorsabbá teszi az alkalmazások és megoldások fejlesztését, és lehetővé teszi az alkalmazások hordozhatóságát IoT Edge eszközök, adatközpontok és a felhő között.

Mi az az Azure SQL Edge videó a Channel 9 platformon:
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/What-is-Azure-SQL-Edge/player]

## <a name="deployment-models"></a>Üzembe helyezési modellek

Az Azure SQL Edge két üzembe helyezési módot támogat.

- Csatlakoztatott üzembe helyezés Azure IoT Edgeon keresztül: az Azure SQL Edge elérhető az Azure piactéren, és modulként üzembe helyezhető [Azure IoT Edgehoz](../iot-edge/about-iot-edge.md). További információ: az [Azure SQL Edge üzembe helyezése](deploy-portal.md).<br>

![SQL Edge – áttekintő diagram](media/overview/overview.png)

- Leválasztott központi telepítés: az Azure SQL Edge-tároló lemezképei a Docker hub-ból tölthetők le, és önálló Docker-tárolóként vagy kubernetes-fürtön is üzembe helyezhetők. További információ: az [Azure SQL Edge üzembe helyezése a Docker-vel](disconnected-deployment.md) és az [Azure SQL Edge-tároló üzembe helyezése a Kubernetes-ben](deploy-kubernetes.md).

## <a name="editions-of-sql-edge"></a>Az SQL Edge kiadása

Az SQL Edge két különböző kiadással vagy szoftver csomaggal érhető el. Ezek a kiadások azonos szolgáltatáskészlet-készlettel rendelkeznek, és csak a használati jogosultságuk, valamint az általuk támogatott CPU-/memória-mennyiség tekintetében térnek el egymástól.

   |**Terv**  |**Leírás**  |
   |---------|---------|
   |Azure SQL Edge-fejlesztő  |  Csak fejlesztési SKU, minden SQL Edge-tároló legfeljebb 4 maggal és 32 GB memóriával rendelkezik  |
   |Azure SQL Edge    |  Üzemi SKU, minden SQL Edge-tároló legfeljebb 8 maggal és 64 GB memóriával rendelkezik. |

## <a name="pricing-and-availability"></a>Díjszabás és rendelkezésre állás

Az Azure SQL Edge már általánosan elérhető. Az egyes régiókban a díjszabással és a rendelkezésre állással kapcsolatos további információkért lásd: az [Azure SQL Edge](https://azure.microsoft.com/services/sql-edge/).

> [!IMPORTANT]
> Az Azure SQL Edge és a SQL Server közötti különbségek megismeréséhez, valamint az Azure SQL Edge különböző lehetőségei közötti különbségekről az [Azure SQL Edge támogatott szolgáltatásai](features.md)című témakörben olvashat.

## <a name="streaming-capabilities"></a>Folyamatos átviteli képességek  

Az Azure SQL Edge a valós idejű elemzések és az összetett események feldolgozásához biztosít beépített folyamatos átviteli képességeket. A folyamatos átviteli képesség ugyanazokkal a szerkezetekkel van felépítve, mint a [Azure stream Analytics](../stream-analytics/stream-analytics-introduction.md) és hasonló képességek, mint a [IoT Edge-eszközökön futó Azure stream Analytics](../stream-analytics/stream-analytics-edge.md).

Az Azure SQL Edge streaming Engine a kis késleltetésű, a rugalmasság, a sávszélesség és a megfelelőség hatékony kihasználására szolgál. 

Az SQL Edge adatátviteli szolgáltatásával kapcsolatos további információkért [tekintse meg az](stream-data.md) adatfolyamot.

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>Machine Learning és mesterséges intelligenciával kapcsolatos képességek

Az Azure SQL Edge beépített gépi tanulási és elemzési funkciókat biztosít a nyílt formátumú ONNX (nyílt neurális hálózati Exchange) futtatókörnyezet integrálásával, amely lehetővé teszi a mély tanulási és neurális hálózati modellek cseréjét a különböző keretrendszerek között. További információ a ONNX-ről: [itt](https://onnx.ai/). A ONNX Runtime rugalmasságot biztosít a modelleknek a választott nyelven vagy eszközökön való fejlesztéséhez, amelyek ezután az SQL Edge-n belüli végrehajtásra alakíthatók át a ONNX formátumba. További információ: [Machine learning és mesterséges intelligencia a ONNX az SQL Edge-ben](onnx-overview.md).

## <a name="working-with-azure-sql-edge"></a>Az Azure SQL Edge használata

Az Azure SQL Edge egyszerűbbé és hatékonyabbá teszi az alkalmazások fejlesztését és karbantartását. A felhasználók az összes ismerős eszközt és szaktudást kihasználva nagyszerű alkalmazásokat és megoldásokat hozhatnak létre a IoT Edge igényeinek megfelelően. A felhasználó az SQL Edge-ben fejlesztheti az alábbi eszközöket:

- [A Azure Portal](https://portal.azure.com/) webalapú alkalmazás az összes Azure-szolgáltatás kezeléséhez.
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) – ingyenes, letölthető ÜGYFÉLALKALMAZÁS bármely SQL-infrastruktúra kezelésére, SQL Serverról a SQL Databasere.
- [SQL Server Data Tools a Visual Studióban](/sql/ssdt/download-sql-server-data-tools-ssdt/) – ingyenes, letölthető ügyfélalkalmazás SQL Server-alapú, SQL Database-adatbázisok, Integration Services-csomagok, Analysis Services adatmodellek és Reporting Services-jelentések fejlesztéséhez.
- [Azure Data Studio](/sql/azure-data-studio/what-is/) – ingyenes, letölthető, platformfüggetlen adatbázis-eszköz, amely a helyszíni és a Felhőbeli adatplatformok Microsoft-családját használja Windows, MacOS és Linux rendszereken.
- [Visual Studio Code](https://code.visualstudio.com/docs) – ingyenes, letölthető, nyílt forráskódú Kódszerkesztő Windows, MacOS és Linux rendszerekhez. Támogatja a bővítményeket, beleértve az [MSSQL bővítményt](https://aka.ms/mssql-marketplace) Microsoft SQL Server, Azure SQL Database és az Azure szinapszis Analytics lekérdezéséhez.


## <a name="next-steps"></a>Következő lépések

- [Az SQL Edge üzembe helyezése Azure Portal](deploy-portal.md)
- [Az SQL Edge Machine Learning és mesterséges intelligencia](onnx-overview.md)
- [Teljes körű IoT-megoldás létrehozása az SQL Edge használatával](tutorial-deploy-azure-resources.md)