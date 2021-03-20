---
title: Azure Stream Analytics Azure Cosmos DB kimenete
description: Ez a cikk azt ismerteti, hogyan lehet adatokat kiadni a Azure Stream Analyticsból a Azure Cosmos DBba.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 9382ac8dc71d1ccb3a85dc0a7a027c8e99296cc6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98016577"
---
# <a name="azure-cosmos-db-output-from-azure-stream-analytics"></a>Azure Stream Analytics Azure Cosmos DB kimenete

A [Azure Cosmos db](https://azure.microsoft.com/services/documentdb/) egy globálisan elosztott adatbázis-szolgáltatás, amely korlátlanul rugalmas méretezhetőséget biztosít a világ minden részén, gazdag lekérdezéssel és automatikus indexeléssel a séma-agnosztikus adatmodellek esetében. Ha többet szeretne megtudni a Stream Analytics-tárolók Azure Cosmos DBéről, tekintse meg a [Stream Analytics Azure Cosmos db kimenetként](stream-analytics-documentdb-output.md) című cikket.

Stream Analytics Azure Cosmos DB kimenete jelenleg nem érhető el az Azure China 21Vianet és az Azure Germany (T-Systems International) régióiban.

> [!Note]
> A Azure Stream Analytics csak az SQL API használatával támogatja a Azure Cosmos DBhoz való kapcsolódást.
> Más Azure Cosmos DB API-k még nem támogatottak. Ha Azure Stream Analytics a más API-kkal létrehozott Azure Cosmos DB-fiókokra mutat, előfordulhat, hogy az adathalmazok nem lesznek megfelelően tárolva.

Az alábbi táblázat a Azure Cosmos DB kimenet létrehozásának tulajdonságait ismerteti.

| Tulajdonság neve | Description |
| --- | --- |
| Kimeneti alias | Az Stream Analytics-lekérdezésben ezt a kimenetet meghívó alias. |
| Sink (Fogadó) | Azure Cosmos DB. |
| Importálási beállítás | Válassza ki **Cosmos db az előfizetésből** , vagy adja meg a **Cosmos db beállításokat manuálisan**.
| Fiókazonosító | A Azure Cosmos DB fiók neve vagy végpontjának URI azonosítója. |
| Fiókkulcs | A Azure Cosmos DB-fiókhoz tartozó megosztott elérési kulcs. |
| Adatbázis | A Azure Cosmos DB adatbázis neve. |
| Tárolónév | A használandó tároló neve, amely a Cosmos DBban léteznie kell. Példa:  <br /><ul><li> _MyContainer_: A "MyContainer" nevű tárolónak léteznie kell.</li>|
| Dokumentum azonosítója |Választható. Annak az elsődleges kulcsnak a megadásához használt mező neve, amelybe az INSERT vagy a Update művelet alapul.

## <a name="partitioning"></a>Particionálás

A partíciós kulcs a lekérdezés PARTITION BY záradékán alapul. A kimeneti írók száma követi a bemeneti particionálást a [teljesen párhuzamos lekérdezéseknél](stream-analytics-scale-jobs.md). Stream Analytics átalakítja a Cosmos DB kimeneti partíciós kulcsát karakterlánccá. Ha például egy bigint típusú 1 értékű partíciós kulccsal rendelkezik, a rendszer a karakterlánc típusú "1" értékre konvertálja. Ez az átalakítás mindig történik, függetlenül attól, hogy a Partition tulajdonságot Cosmos DBba írta-e.

## <a name="output-batch-size"></a>Kimeneti köteg mérete

Az üzenetek maximális méretéhez lásd: [Azure Cosmos db korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cosmos-db-limits). A köteg mérete és az írási gyakoriság a Azure Cosmos DB válaszok alapján dinamikusan módosul. A Stream Analytics nem rendelkezik előre meghatározott korlátozásokkal.

## <a name="next-steps"></a>Következő lépések

* [Útmutató: Stream Analytics-feladat létrehozása az Azure Portal használatával](stream-analytics-quick-create-portal.md)
* [Gyors útmutató: Azure Stream Analytics-feladatok létrehozása az Azure CLI használatával](quick-create-azure-cli.md)
* [Gyors útmutató: Azure Stream Analytics-feladatok létrehozása ARM-sablon használatával](quick-create-azure-resource-manager.md)
* [Gyors útmutató: Stream Analytics-feladatok létrehozása Azure PowerShell használatával](stream-analytics-quick-create-powershell.md)
* [Rövid útmutató: Azure Stream Analytics-feladatok létrehozása a Visual Studio használatával](stream-analytics-quick-create-vs.md)
* [Rövid útmutató: Azure Stream Analytics-feladatok létrehozása a Visual Studio Code-ban](quick-create-visual-studio-code.md)
