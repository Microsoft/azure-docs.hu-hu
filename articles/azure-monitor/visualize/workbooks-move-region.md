---
title: Azure Monitor munkafüzetek – régiók áthelyezése
description: Munkafüzet áthelyezése másik régióba
services: azure-monitor
author: gardnerjr
manager: acearun
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/12/2020
ms.author: jgardner
ms.openlocfilehash: d58d9ab48fa16fb5258f097ed4567e539e21c72c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100615978"
---
# <a name="move-an-azure-workbook-to-another-region"></a>Azure-munkafüzetek áthelyezése másik régióba

Ez a cikk azt ismerteti, hogyan helyezhetők át az Azure-munkafüzetek erőforrásai egy másik Azure-régióba. Az erőforrásokat több okból is áthelyezheti egy másik régióba. Ha például egy új Azure-régió előnyeit szeretné kihasználni, csak meghatározott régiókban elérhető szolgáltatásokat vagy szolgáltatásokat telepíthet, a belső házirend-és irányítási követelmények teljesítéséhez, vagy a kapacitás megtervezésének követelményeire reagálva.

## <a name="prerequisites"></a>Előfeltételek

* Győződjön meg arról, hogy a megcélzott régióban a munkafüzetek támogatottak.

* Ezek az utasítások mind a közös munkafüzetek ( `microsoft.insights/workbooks` ), mind a privát munkafüzetek ( `microsoft.insights/myworkbooks` ) Azure monitor és a legtöbb erőforrástípus esetében érvényesek.

  A Application Insights erőforrástípus számára kifejezetten csatolt munkafüzetek esetében azonban ezek a munkafüzetek abban az Azure-régióban tárolódnak, ahol a Application Insights erőforrás mentve van.

  Ezek a munkafüzetek nem helyezhetők el egyenként másik régióba.

## <a name="move"></a>Áthelyezés

Az Azure-munkafüzetek áthelyezésének legegyszerűbb módja, ha a portál felhasználói felületén a "Mentés másként" lehetőséget használja a munkafüzetek eszközben:

1. Nyissa meg a cél munkafüzetet a munkafüzet-megjelenítőben.
2. A szerkesztési mód megadásához használja az eszköztár szerkesztése gombot.
3. Használja a "Mentés másként" eszközsor gombot.
4. A Save (Mentés) űrlapon válassza ki a munkafüzet nevét és a kívánt régiót. Győződjön meg arról, hogy az előfizetés, az erőforráscsoport és a megosztás egyéb mezői megfelelőek.

   > [!NOTE]
   > Előfordulhat, hogy a munkafüzethez új nevet kell használnia az ismétlődő nevek elkerülése érdekében.

5. Mentse el. 

## <a name="verify"></a>Ellenőrzés

Az új munkafüzet megkereséséhez használja az Azure-munkafüzetek Tallózás felhasználói felületét. Győződjön meg arról, hogy a hely a célhely.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Miután létrehozta a munkafüzetet az új régióban, törölje az eredeti munkafüzetet az előző régióban.
1. Nyissa meg az eredeti munkafüzetet a munkafüzet-megjelenítőben.
2. A szerkesztési mód megadásához használja az eszköztár szerkesztése gombot.
3. Az eszközök szerkesztése legördülő menüből (ceruza ikon) válassza a "munkafüzet törlése" lehetőséget.

Ha átnevezte a munkafüzetet egy új régióba való importáláshoz, akkor a munkafüzetet a korábbi névre is átnevezheti az eredeti munkafüzet törlése után, a szerkesztési mód eszköztár Szerkesztés eszközeinek legördülő menü "Átnevezés" elemének használatával.

## <a name="next-steps"></a>Következő lépések

Munkafüzet helyett egy munkafüzetet kell áthelyeznie? Lásd: [Azure munkafüzet-sablon áthelyezése másik régióba](./workbook-templates-move-region.md).

Lásd: [munkafüzetek és munkafüzet-sablonok üzembe helyezése ARM-](../visualize/workbooks-automate.md) sablonok használatával.
