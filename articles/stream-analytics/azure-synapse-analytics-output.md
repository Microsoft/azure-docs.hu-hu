---
title: Az Azure szinapszis Analytics kimenete Azure Stream Analytics
description: Ez a cikk az Azure szinapszis Analytics-t ismerteti Azure Stream Analytics-kimenetként.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 65752321f94bfa1403839889ead284d46b5f7191
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/23/2020
ms.locfileid: "97740062"
---
# <a name="azure-synapse-analytics-output-from-azure-stream-analytics"></a>Az Azure szinapszis Analytics kimenete Azure Stream Analytics

Az [Azure szinapszis Analytics](https://azure.microsoft.com/services/synapse-analytics) egy korlátlan elemzési szolgáltatás, amely egyesíti a vállalati adattárházat és a Big adatelemzést. 

Azure Stream Analytics feladatok kimenete egy dedikált SQL Pool-táblázatba kerül az Azure szinapszis Analyticsben, és képes feldolgozni az átviteli sebességet akár 200 MB/s-ra. Ez támogatja a legigényesebb valós idejű elemzési és a gyors elérésű adatfeldolgozási igényeket olyan számítási feladatokhoz, mint a jelentéskészítés és az irányítópult.  

A dedikált SQL Pool-táblának léteznie kell ahhoz, hogy kimenetként hozzá lehessen adni a Stream Analytics feladathoz. A tábla sémájának meg kell egyeznie a feladatok kimenetében szereplő mezőkkel és típusokkal. 

Ha az Azure Szinapszisot kimenetként szeretné használni, győződjön meg arról, hogy a Storage-fiók konfigurálva van. Navigáljon a Storage-fiók beállításaihoz a Storage-fiók konfigurálásához. Csak a táblákat támogató Storage-fiókok engedélyezettek: általános célú v2 és általános célú v1. Csak a standard szintű csomag kiválasztása. A prémium szint nem támogatott.

## <a name="output-configuration"></a>Kimeneti konfiguráció

A következő táblázat felsorolja a tulajdonságok nevét és leírásait az am Azure szinapszis Analytics kimenetének létrehozásához.

|Tulajdonság neve|Leírás|
|-|-|
|Kimeneti alias |Egy rövid név, amely a lekérdezésekben a lekérdezés kimenetének az adatbázisba való irányításához használatos. |
|Adatbázis |a dedikált SQL-címkészlet neve, ahová a kimenetet küldi. |
|Kiszolgálónév |Az Azure szinapszis-kiszolgáló neve.  |
|Felhasználónév |Az adatbázishoz írási hozzáféréssel rendelkező Felhasználónév. A Stream Analytics csak az SQL-hitelesítést támogatja. |
|Jelszó |Az adatbázishoz való kapcsolódáshoz használt jelszó. |
|Táblázat  | A tábla neve, ahol a kimenet meg van írva. A tábla neve megkülönbözteti a kis-és nagybetűket. A táblázat sémájának pontosan meg kell egyeznie a feladatok által létrehozott mezők és típusok számával.|

## <a name="next-steps"></a>További lépések

* [Felügyelt identitások használata Azure SQL Database vagy Azure szinapszis Analytics eléréséhez egy Azure Stream Analytics feladatokból (előzetes verzió)](sql-database-output-managed-identity.md)
* [Útmutató: Stream Analytics-feladat létrehozása az Azure Portal használatával](stream-analytics-quick-create-portal.md)