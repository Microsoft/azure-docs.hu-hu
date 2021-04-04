---
title: Integrált megoldások készítése
description: Megoldási eszközök és partnerek, amelyek egy dedikált SQL-készlettel (korábban SQL DW) integrálhatók az Azure szinapszis Analytics szolgáltatásban.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 3e55ef054d5c305937f88d6ec5b2b4453cac6792
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98117758"
---
# <a name="integrate-other-services-with-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Más szolgáltatások integrálása dedikált SQL-készlettel (korábban SQL DW) az Azure szinapszis Analytics szolgáltatásban.

Az Azure szinapszis Analytics dedikált SQL-készlete (korábban SQL DW) funkciója lehetővé teszi a felhasználók számára az Azure számos más szolgáltatásának integrálását. A dedikált SQL-készlet több további szolgáltatást is használhat, amelyek némelyike többek között a következőkből áll:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

Az Azure-beli integrációs szolgáltatásokkal kapcsolatos további információkért tekintse át az [integrációs partnerek](sql-data-warehouse-partner-data-integration.md)című cikket.

## <a name="power-bi"></a>Power BI

Az Power BI integráció lehetővé teszi az adatraktár számítási teljesítményének összevonását a Power BI dinamikus jelentéskészítésével és vizualizációval. A Power BI-integráció jelenleg a következőket tartalmazza:

* **Közvetlen** kapcsolat: a dedikált SQL-készlet (korábbi NEVÉN SQL DW) használatával létrehozott adattárházak logikai pushdown való fejlettebb kapcsolata. A leküldés gyorsabb elemzést biztosít, nagyobb léptékben.
* **Megnyitás a Power BIban**: a "Megnyitás Power bi" gomb a példányok információit átadja a Power BInak a kapcsolódás egyszerűsített módjaként.

További információ: Integration [with Power bi](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect), vagy a [Power bi dokumentációja](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory a felhasználók számára felügyelt platformot biztosít összetett kinyerési és betöltési folyamatok létrehozásához. A dedikált SQL-készlet (korábbi nevén SQL DW) integrációja Azure Data Factory tartalmazza a következőket:

* **Tárolt eljárások**: a tárolt eljárások végrehajtásának összehangolása.
* **Másolás**: használja az ADF-et az adat dedikált SQL-készletbe (korábban SQL DW) való áthelyezéséhez. Ez a művelet az ADF standard adatáthelyezési mechanizmusát vagy a borítók alá tartozó albaset is használhatja.

További információ: [integráció a Azure Data Factorysal](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning

A Azure Machine Learning egy teljes körűen felügyelt elemzési szolgáltatás, amely lehetővé teszi, hogy bonyolult modelleket hozzon létre a prediktív eszközök nagy készletével. A dedikált SQL-készlet (korábban SQL DW) a modellek forrásaként és célként is támogatott, és a következő funkciókkal rendelkezik:

* **Olvasási információk:** A T-SQL-T használó méretezési modellek a dedikált SQL-készlet (korábban SQL DW) használatával méretezhetők.
* **Írási érték:** A módosításokat bármely modellből visszavéglegesítheti egy dedikált SQL-készletbe (korábban SQL DW).

További információ: [integráció a Azure Machine Learningsal](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

A Azure Stream Analytics egy összetett, teljes körűen felügyelt infrastruktúra az Azure Event hub-ból generált események feldolgozásához és felhasználásához.  A dedikált SQL-készlettel (korábban SQL DW) való integráció lehetővé teszi, hogy az adatfolyamok hatékonyan legyenek feldolgozva és tárolva a jobb és összetettebb elemzést lehetővé tévő kapcsolatok mellett.  

* **Feladatok kimenete:** Kimenet küldése Stream Analytics feladatokból közvetlenül egy dedikált SQL-készletbe (korábban SQL DW).

További információ: [integráció a Azure stream Analyticssal](sql-data-warehouse-integrate-azure-stream-analytics.md).