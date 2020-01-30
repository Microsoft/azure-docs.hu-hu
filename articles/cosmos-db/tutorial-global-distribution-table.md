---
title: A Table API Azure Cosmos DB globális terjesztési oktatóanyaga
description: Ismerje meg, hogyan működik a globális terjesztés a Azure Cosmos DB Table API-fiókokban, és hogyan konfigurálhatja a régiók előnyben részesített listáját
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/02/2019
ms.reviewer: sngun
ms.openlocfilehash: 148e17edbb8be566db611216f444fedad514e638
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76770586"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Az Azure Cosmos DB globális terjesztésének beállítása a Table API-val

Ez a cikk a következő feladatokat mutatja be: 

> [!div class="checklist"]
> * Globális terjesztés konfigurálása az Azure Portallal
> * Globális terjesztés konfigurálása a [Table API](table-introduction.md) használatával

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Csatlakozás egy kívánt régióhoz a Table API használatával

A [globális terjesztés](distribute-data-globally.md) kihasználása érdekében az ügyfélalkalmazások megadhatják a preferált régiók sorrendbe rendezett listáját a dokumentumokkal kapcsolatos műveletek elvégzéséhez. Ezt a [TableConnectionPolicy.PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet) tulajdonság beállításával lehet megtenni. Az Azure Cosmos DB Table API SDK a kommunikációhoz az optimális végpontot a fiók konfigurációja, az aktuális régiónkénti rendelkezésre állás és a megadott preferencialista alapján választja ki.

A PreferredLocations tulajdonságnak egy vesszővel elválasztott listát kell tartalmaznia az előnyben részesített (többkiszolgálós) helyekről az olvasásokhoz. Minden ügyfélpéldány a kívánt sorrendben adhatja meg ezen régiók részhalmazát a kis késleltetésű olvasásokhoz. A régiókat a [megjelenített nevükkel](https://msdn.microsoft.com/library/azure/gg441293.aspx) kell elnevezni, például: `West US`.

Az olvasások a PreferredLocations lista első elérhető régiójába lesznek küldve. Ha a kérelem meghiúsul, az ügyfél továbbadja a listát a következő régiónak, és így tovább.

Az SDK a PreferredLocations listában szereplő régiókból próbál meg olvasni. Így ha például az adatbázisfiók három régióban érhető el, de az ügyfél csak kettőt ad meg a nem írási régiók közül a PreferredLocations listában, akkor az írási régió nem szolgál ki olvasásokat, még feladatátvétel esetén sem.

Az SDK az összes írást automatikusan az aktuális írási régióba küldi.

Ha a PreferredLocations tulajdonság nincs beállítva, a kérelmek az aktuális írási régióból lesznek teljesítve.

Ezzel el is végezte az oktatóanyagot. Ha meg szeretné ismerni, hogyan kezelheti a globálisan replikált fiók konzisztenciáját, olvassa el a [Konzisztenciaszintek az Azure Cosmos DB-ben](consistency-levels.md) című cikket. További információ a globális adatbázis-replikáció működéséről az Azure Cosmos DB szolgáltatásban: [Globális adatterjesztés az Azure Cosmos DB-vel](distribute-data-globally.md).

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a következőket hajtotta végre:

> [!div class="checklist"]
> * Globális terjesztés konfigurálása az Azure Portallal
> * Globális terjesztés konfigurálása az Azure Cosmos DB Table API-jaival

