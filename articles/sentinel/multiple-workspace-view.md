---
title: Azure Sentinel-incidensek használata egyszerre sok munkaterületen | Microsoft Docs
description: Az incidensek megtekintése több munkaterületen párhuzamosan az Azure Sentinelben.
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
ms.date: 04/20/2020
ms.author: yelevin
ms.openlocfilehash: 448998328ff15b74b0aa0b17e2435a7ff55c54a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "83124175"
---
# <a name="work-with-incidents-in-many-workspaces-at-once"></a>Incidensek használata egyszerre sok munkaterületen 

 Az Azure Sentinel képességeinek kihasználása érdekében a Microsoft egyetlen munkaterület használatát javasolja. Vannak azonban olyan használati esetek is, amelyek több munkaterületet igényelnek, bizonyos esetekben – például egy [felügyelt biztonsági szolgáltató (MSSP)](./multiple-tenants-service-providers.md) és ügyfelei számára – több bérlőn keresztül. **Több munkaterület-nézet** lehetővé teszi, hogy a biztonsági incidenseket több munkaterületen is megtekintheti és használhatja egyszerre, akár bérlők között is, így a szervezete biztonságának teljes láthatóságát és felügyeletét is megtarthatja.

## <a name="entering-multiple-workspace-view"></a>Több munkaterület-nézet megadása

Az Azure Sentinel megnyitásakor megjelenik azon munkaterületek listája, amelyekhez hozzáférési jogokkal rendelkezik az összes kiválasztott bérlőn és előfizetésen belül. Az egyes munkaterületek nevének bal oldalán egy jelölőnégyzet látható. Egy adott munkaterület nevére kattintva megnyílik a munkaterületen. Több munkaterület kiválasztásához kattintson az összes megfelelő jelölőnégyzetre, majd kattintson az oldal tetején található **több munkaterület nézet** gombra.

> [!IMPORTANT]
> Több munkaterület-nézet jelenleg legfeljebb 10 egyidejű megjelenített munkaterületet támogat. 
> 
> Ha több mint 10 munkaterületet ad meg, a rendszer figyelmeztető üzenetet jelenít meg.

Vegye figyelembe, hogy a munkaterületek listájában megtekintheti az egyes munkaterületekhez társított könyvtárat, előfizetést, helyet és erőforráscsoportot. A könyvtár megfelel a Bérlőnek.

   ![Több munkaterület kiválasztása](./media/multiple-workspace-view/workspaces.png)

## <a name="working-with-incidents"></a>Incidensek használata

**Több munkaterület nézetben** csak az **incidensek** képernyő érhető el. Úgy néz ki és működik, mint a szokásos **incidensek** képernyője. Van néhány fontos különbség, bár:

   ![Incidensek megtekintése több munkaterületen](./media/multiple-workspace-view/incidents.png)

- A lap tetején lévő számlálók – *nyitott incidensek*, *új incidensek*, *folyamatban* stb. – a kiválasztott munkaterületek számait együttesen jelenítik meg.

- A kiválasztott munkaterületekről és címtárakról (bérlők) származó incidensek egyetlen egységesített listában jelennek meg. A listát a normál **incidensek** képernyőjén lévő szűrők mellett a munkaterület és a könyvtár alapján is szűrheti.

- Olvasási és írási engedélyekkel kell rendelkeznie az összes olyan munkaterületről, amelyről kiválasztotta az incidenseket. Ha csak olvasási jogosultsággal rendelkezik egyes munkaterületeken, a rendszer figyelmeztető üzeneteket jelenít meg, ha az incidensek lehetőséget választja a munkaterületeken. Ezekkel az incidensekkel vagy másokkal együtt Ön nem módosítható (még akkor is, ha rendelkezik a többire vonatkozó engedélyekkel).

- Ha egyetlen incidenst választ, és a **teljes részletek megtekintése** vagy a **vizsgálat** lehetőségre kattint, akkor az adott incidens munkaterületének adatkörnyezetében lesz, mások pedig nem.

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtudhatta, hogyan lehet párhuzamosan megtekinteni és feldolgozni az incidenseket több Azure Sentinel-munkaterületen. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).

