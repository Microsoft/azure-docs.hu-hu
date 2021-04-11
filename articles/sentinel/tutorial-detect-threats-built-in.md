---
title: Az Azure Sentinel beépített elemzési szabályaival kapcsolatos fenyegetések észlelése | Microsoft Docs
description: Megtudhatja, hogyan használhatók a veszélyforrások észlelésére szolgáló beépített sablonok, amelyek értesítik, ha valamilyen gyanús esemény történik.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2021
ms.author: yelevin
ms.openlocfilehash: 951c616961ff68b810ca135d09a6f6253cb2b7ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104773555"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>Oktatóanyag: Használatra kész veszélyforrás-észlelés

Miután [csatlakoztatta az adatforrásokat](quickstart-onboard.md) az Azure Sentinel szolgáltatáshoz, értesítést szeretne kapni, ha valamilyen gyanús esemény történik. Az Azure Sentinel ezért beépített, beépített sablonokat tartalmaz, amelyek segítenek a fenyegetések észlelési szabályainak létrehozásában. Ezeket a sablonokat az ismert fenyegetések, a gyakori támadási vektorok és a gyanús tevékenység-eszkalációs láncok alapján a Microsoft biztonsági szakértői és elemzői alkották. Az ezekből a sablonokból létrehozott szabályok automatikusan megkeresik a környezetet minden olyan tevékenység esetében, amely gyanúsnak tűnik. A sablonok többsége testreszabható a tevékenységek keresésére, vagy az igényeinek megfelelő szűrésre. Az ezekkel a szabályokkal létrehozott riasztások olyan incidenseket hoznak létre, amelyeket a környezetben rendelhet hozzá és vizsgálhat meg.

Ez az oktatóanyag segítséget nyújt a fenyegetések észleléséhez az Azure Sentinel használatával:

> [!div class="checklist"]
> * Beépített veszélyforrások észlelése
> * Fenyegetési válaszok automatizálása

## <a name="about-out-of-the-box-detections"></a>A beépített észlelések ismertetése

Az összes beépített észlelés megtekintéséhez lépjen az **elemzés** , majd a **szabály sablonok** elemre. Ezen a lapon az Azure Sentinel beépített szabályai láthatók.

   :::image type="content" source="media/tutorial-detect-built-in/view-oob-detections.png" alt-text="A fenyegetések felderítése az Azure Sentinel használatával beépített észlelésekkel":::

A következő sablon típusok érhetők el:

- **Microsoft-Biztonság**
   
   A Microsoft biztonsági sablonjai valós időben hoznak létre automatikusan Azure Sentinel-incidenseket a más Microsoft biztonsági megoldásokban létrehozott riasztásokból. A Microsoft biztonsági szabályai sablonként használhatók a hasonló logikával rendelkező új szabályok létrehozásához. További információ a biztonsági szabályokról: [incidensek automatikus létrehozása a Microsoft biztonsági értesítéseiből](create-incidents-from-alerts.md).

- **Fúziós** 

    A fúziós technológián alapuló, fejlett, többszintű támadások észlelése az Azure Sentinel-ben skálázható gépi tanulási algoritmusokat használ, amelyek számos kis-és nagyszámú riasztást és eseményt kezelhetnek több termékben, amelyek magas szintű és gyakorlati incidensekkel rendelkeznek A Fusion alapértelmezés szerint engedélyezve van. Mivel a logika rejtett, ezért nem szabható testre, csak egy szabályt hozhat létre ezzel a sablonnal.

    > [!IMPORTANT]
    > A fúziós szabály sablonjának néhány észlelése jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.
    >
    > Ha szeretné megtekinteni, hogy mely észlelések vannak előzetes verzióban, tekintse meg [a fejlett többlépcsős támadás észlelését az Azure sentinelben](fusion.md).

- **Gépi tanulási viselkedéselemzés**

    Ezek a sablonok a Microsoft gépi tanulási algoritmusai alapján működnek, így nem látja a belső logikát a működésük és a futtatásuk során. Mivel a logika rejtett, ezért nem szabható testre, csak egyetlen szabályt hozhat létre minden ilyen típusú sablonnal.

    > [!IMPORTANT]
    > - A Machine learning viselkedési elemzési szabály sablonjai jelenleg **előzetes** verzióban érhetők el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.
    >
    > - Az ML-viselkedési elemzési sablonokon alapuló szabályok létrehozásával és engedélyezésével lehetővé teszi a Microsoft számára, hogy a gépi tanulási motorok és modellek feldolgozásához szükséges módon **másolja az Azure Sentinel-munkaterület földrajzán kívüli** betöltött adatot.

- **Ütemezett**

    Az ütemezett elemzési szabályok a Microsoft biztonsági szakértői által írt beépített lekérdezéseken alapulnak. Láthatja a lekérdezési logikát, és módosíthatja azt. Az ütemezett szabályok sablont használhatja, és testreszabhatja a lekérdezési logikát és az ütemezési beállításokat új szabályok létrehozásához.

## <a name="use-out-of-the-box-detections"></a>Beépített észlelések használata

1. Ha beépített sablont szeretne használni, kattintson a sablon nevére, majd kattintson a **szabály létrehozása** gombra a részletek ablaktáblán a sablon alapján létrehozott új aktív szabály létrehozásához. Minden sablon rendelkezik a szükséges adatforrások listájával. A sablon megnyitásakor az adatforrások automatikusan bekerülnek a rendelkezésre állásba. Ha rendelkezésre állási probléma merül fel, előfordulhat, hogy a **szabály létrehozása** gomb le van tiltva, vagy erre figyelmeztető üzenet jelenik meg.
  
    :::image type="content" source="media/tutorial-detect-built-in/use-built-in-template.png" alt-text="Észlelési szabály előnézet panelje":::
 
1. A **szabály létrehozása** gombra kattintva megnyílik a szabály létrehozása varázsló a kiválasztott sablon alapján. Az összes adat kitöltése, valamint az **ütemezett** vagy a **Microsoft biztonsági** sablonjaival testreszabhatja a logikát és az egyéb szabályok beállításait, hogy jobban megfeleljen az igényeinek. Ezt a folyamatot megismételve további szabályokat hozhat létre a beépített sablon alapján. A szabály létrehozása varázsló lépéseinek befejezését követően a sablon alapján elkészült egy szabály létrehozása. Az új szabályok az **aktív szabályok** lapon fognak megjelenni.

    További információ a szabályok testreszabásáról a szabály létrehozása varázslóban [: oktatóanyag: egyéni elemzési szabályok létrehozása a fenyegetések észleléséhez](tutorial-detect-threats-custom.md).

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megtudhatta, hogyan kezdheti el a fenyegetések észlelését az Azure Sentinel használatával. 

Ha szeretné megtudni, hogyan automatizálhatja a fenyegetésekre adott válaszokat, [állítson be automatizált fenyegetési válaszokat az Azure sentinelben](tutorial-respond-threats-playbook.md).
