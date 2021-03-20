---
title: A Batch szolgáltatás munkafolyamata és erőforrásai
description: Ismerje meg a Batch szolgáltatás funkcióit és a fejlesztési szempontból magas szintű munkafolyamatát.
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: 76a0b140f3bea4b07a6de632abbcbc3fd26e582d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85965212"
---
# <a name="batch-service-workflow-and-resources"></a>A Batch szolgáltatás munkafolyamata és erőforrásai

A Azure Batch szolgáltatás alapvető összetevőinek áttekintésében megbeszéljük azt a magas szintű munkafolyamatot, amelyet a Batch-fejlesztők nagy léptékű párhuzamos számítási megoldások létrehozásához használhatnak, valamint a használt elsődleges szolgáltatási erőforrásokat is.

Akár olyan elosztott számítási alkalmazást vagy szolgáltatást fejleszt, amely közvetlen [REST API](/rest/api/batchservice/) hívásokat hajt végre, vagy ha a [Batch SDK](batch-apis-tools.md#batch-service-apis)-k valamelyikét használja, az itt ismertetett erőforrások és szolgáltatások nagy részét fogja használni.

> [!TIP]
> A Batch szolgáltatás részletesebb leírásáért olvassa el a következő cikket: [Basics of Azure Batch](batch-technical-overview.md) (Az Azure Batch alapjai). Lásd továbbá a [Batch szolgáltatás legújabb frissítéseit](https://azure.microsoft.com/updates/?product=batch).

## <a name="basic-workflow"></a>Alapvető munkafolyamat

Az alább olvasható elméleti szintű munkafolyamat gyakorlatilag a Batch szolgáltatást párhuzamos számítási feladatok feldolgozására használó összes alkalmazásra és szolgáltatásra érvényes:

1. Töltse fel a feldolgozni kívánt **adatfájlokat** egy [Azure Storage](../storage/index.yml)-fiókba. A Batch beépített támogatást biztosít az Azure Blob-tárhelyekhez, így a tevékenységek futtatásakor a rendszer le tudja tölteni a fájlokat a [számítási csomópontokra](nodes-and-pools.md#nodes).
2. Töltse fel az **alkalmazásfájlokat**, amelyeket a tevékenységek futtatni fognak. Ezek lehetnek bináris fájlok vagy parancsfájlok, illetve ezek függőségei, futtatásukat a feladatokban lévő tevékenységek végzik. A tevékenységek képesek letölteni ezeket a fájlokat az Ön tárfiókjából, de az alkalmazások felügyeletére és üzembe helyezésére a Batch [alkalmazáscsomagok](nodes-and-pools.md#application-packages) funkcióját is használhatja.
3. Hozzon létre egy számítási csomópontok [készletét](nodes-and-pools.md#pools) . A készlet létrehozásakor meg kell adnia a készletbe tartozó számítási csomópontok számát, méretét, valamint a használt operációs rendszer típusát. Amikor a rendszer lefuttatja a feladatokat, végrehajtásuk céljából hozzájuk rendeli a készlet egyik csomópontját.
4. Hozzon létre egy [feladatot](jobs-and-tasks.md#jobs). A feladatok tevékenységek gyűjteményeit kezelik. Az egyes feladatokat konkrét készlethez kell rendelni; itt fognak lefutni a feladathoz tartozó tevékenységek.
5. [Tevékenységek](jobs-and-tasks.md#tasks) hozzáadása a feladathoz. A tevékenységek lefuttatják az Ön által korábban feltöltött alkalmazást vagy parancsfájlt, és feldolgozzák a tárfiókból letöltött adatfájlokat. Amikor befejeződnek a tevékenységek, feltöltik az eredményüket az Azure Storage-fiókba.
6. Kövesse figyelemmel a tevékenység előrehaladását, és kérje le a feladatok eredményét az Azure Storage-fiókból.

> [!NOTE]
> A Batch szolgáltatás használatához [Batch-fiókra](accounts.md) van szüksége. A legtöbb Batch-megoldás esetében szükség van egy társított [Azure Storage](../storage/index.yml)-fiókra is a fájlok tárolásához és lekéréséhez.

## <a name="batch-service-resources"></a>A Batch szolgáltatáshoz szükséges erőforrások

Az alábbi témakörök ismertetik az elosztott számítási forgatókönyveket lehetővé tevő batch erőforrásait.

- [Batch-fiókok és Storage-fiókok](accounts.md)
- [Csomópontok és készletek](nodes-and-pools.md)
- [Feladatok és tevékenységek](jobs-and-tasks.md)
- [Fájlok és könyvtárak](files-and-directories.md)

## <a name="next-steps"></a>Következő lépések

- Megismerheti a Batch-megoldások fejlesztéséhez rendelkezésre álló [Batch API-kat és eszközöket](batch-apis-tools.md).
- Megismerheti a Batch-kompatibilis alkalmazások [Batch .NET ügyfélkönyvtárral](quick-run-dotnet.md) vagy [Python](quick-run-python.md) segítségével való fejlesztésének alapjait. Ezek a rövid útmutatók végigvezetik egy mintaalkalmazáson, amely a Batch szolgáltatással futtat egy számítási feladatot több számítási csomóponton, és az Azure Storage szolgáltatást is használja a számítási feladatok fájljainak előkészítéséhez és lekéréséhez.
- Töltse le és telepítse a [Batch Explorer](https://azure.github.io/BatchExplorer/) alkalmazást, amelyet bármikor használhat, ha a Batch-megoldások fejlesztése során segítségre van szüksége. A Batch Explorer alkalmazással Azure Batch-alkalmazásokat hozhat létre és monitorozhat, valamint a felmerülő hibáikat is elháríthatja.
- Lásd: közösségi erőforrások, többek között a [stack overflow](https://stackoverflow.com/questions/tagged/azure-batch), a [Batch közösségi](https://github.com/Azure/Batch)tárház és a [Azure batch fórum](/answers/topics/azure-batch.html).
