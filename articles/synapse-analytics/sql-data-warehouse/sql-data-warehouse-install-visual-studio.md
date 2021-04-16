---
title: A 2019 Visual Studio telepítése
description: A Visual Studio és SQL Server eszközök (SSDT) telepítése a Synapse SQL
services: synapse-analytics
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/11/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.openlocfilehash: c8c07997b3ef8cb050ea4609a650a3e3b1bd21fb
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568250"
---
# <a name="getting-started-with-visual-studio-2019"></a>Ismerkedés a Visual Studio 2019-cel

Visual Studio **2019** SQL Server Data Tools (SSDT) egyetlen eszköz, amely a következőket teszi lehetővé:

- Alkalmazások csatlakoztatása, lekérdezése és fejlesztése
- Az objektumkezelővel vizuálisan feltárhatja az adatmodell összes objektumát, beleértve a táblákat, nézeteket, tárolt eljárásokat stb.
- T-SQL-adatdefiníciós nyelvi (DDL-) szkriptek létrehozása az objektumokhoz
- Adattárház fejlesztése állapotalapú megközelítéssel SSDT-adatbázisprojektekkel
- Az adatbázisprojekt integrálása olyan verzióvezérlő rendszerekkel, mint a Git és az Azure Repos
- Folyamatos integrációs és üzembe helyezési folyamatok beállítása olyan automatizálási kiszolgálókkal, mint az Azure DevOps

## <a name="install-visual-studio-2019"></a>A 2019 Visual Studio telepítése

A 16.3-as vagy Visual Studio letöltésével és telepítésével Visual Studio lásd: Download [Visual Studio 2019 (A 2019-es](https://visualstudio.microsoft.com/downloads/) Visual Studio **letöltése).** A telepítés során válassza ki az adattárolási és -feldolgozási számítási feladatot. A 2019-es Visual Studio önálló SSDT telepítésére már nincs szükség.

## <a name="unsupported-features-in-ssdt"></a>Nem támogatott szolgáltatások az SSDT-ben

Előfordulhat, hogy az ssdT Synapse SQL funkciók kiadása nem tartalmazza. A következő funkciók jelenleg nem támogatottak:


- [Számítási feladatok kezelése](sql-data-warehouse-workload-management.md) – számítási feladatok csoportjai és osztályozói
- [Sorszintű biztonság](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (beleértve a tábla értékű függvényeket)
  - Küldjön támogatási [jegyet, vagy szavazzon a](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) funkció támogatásához.
  - Küldjön támogatási [jegyet, vagy szavazzon a](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) funkció támogatásához.
- Bizonyos T-SQL-funkciók, például:
   - *A GROUP záradék* a [STRING_AGG](/sql/t-sql/functions/string-agg-transact-sql) sztring függvényben.

## <a name="next-steps"></a>Következő lépések

Most, hogy az SSDT legújabb verziójával rendelkezik, készen áll az [SQL-készlethez](sql-data-warehouse-query-visual-studio.md) való csatlakozásra.