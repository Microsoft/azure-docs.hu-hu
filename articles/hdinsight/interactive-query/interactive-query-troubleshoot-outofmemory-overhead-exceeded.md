---
title: A Apache Hive OutOfMemory-hibába való csatlakozása az Azure HDInsight
description: A OutOfMemory hibák kezelése "a GC rezsi-korlátja túllépte a hibát"
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 0c396cde38d8cba8e1f3eaf8527429647868a0c8
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935957"
---
# <a name="scenario-joins-in-apache-hive-leads-to-an-outofmemory-error-in-azure-hdinsight"></a>Forgatókönyv: a Apache Hive lévő illesztések az Azure HDInsight OutOfMemory-hibába vezetnek.

Ez a cikk az interaktív lekérdezési összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Apache Hive illesztések alapértelmezett viselkedése egy tábla teljes tartalmának betöltése a memóriába, így a csatlakozás a Térkép/csökkentés lépés végrehajtása nélkül is elvégezhető. Ha a kaptár tábla túl nagy a memóriához való illeszkedéshez, a lekérdezés sikertelen lehet.

## <a name="cause"></a>Ok

Ha az illesztések megfelelő méretű struktúrában futnak, a rendszer a következő hibát észlelte:

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded error.
```

## <a name="resolution"></a>Feloldás

A következő struktúra konfigurációs értékének beállításával megakadályozhatja, hogy a struktúra betöltsön a táblákat a memóriába az illesztések között (egy Térkép/csökkentési lépés végrehajtása helyett):

```
hive.auto.convert.join=false
```

## <a name="next-steps"></a>Következő lépések

Ha a beállítás értéke nem oldja meg a problémát, látogasson el a következő témakör egyikére...

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az [@AzureSupport](https://twitter.com/azuresupport) Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](../../azure-portal/supportability/how-to-create-azure-support-request.md)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.