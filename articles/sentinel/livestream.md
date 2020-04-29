---
title: A fenyegetések észlelése az Azure Sentinelben a "Hunting livestream" használatával | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használható a Hunting livestream az Azure Sentinelben az adatnyomon követéshez.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2019
ms.author: yelevin
ms.openlocfilehash: b392644e504fa8187e637278bef8718c9c2caa3f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77582126"
---
# <a name="use-hunting-livestream-in-azure-sentinel-to-detect-threats"></a>A fenyegetések észlelése az Azure Sentinelben

> [!IMPORTANT]
> A livestream in Azure Sentinel szolgáltatás jelenleg nyilvános előzetes verzióban érhető el, és fokozatosan zajlik a bérlők számára.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


A Hunting livestream használatával interaktív munkameneteket hozhat létre, amelyek lehetővé teszik az újonnan létrehozott lekérdezések tesztelését az események bekövetkezésekor, értesítéseket kaphat a munkamenetekről, ha egyezést talál, és szükség esetén elindítja a vizsgálatokat. Gyorsan létrehozhat egy livestream-munkamenetet bármilyen Log Analytics lekérdezés használatával.

- **Újonnan létrehozott lekérdezések tesztelése események bekövetkezésekor**
    
    A lekérdezéseket tesztelheti és módosíthatja úgy, hogy ne legyenek ütközések az eseményekre aktívan alkalmazott szabályokkal. Miután megerősítette, hogy ezek az új lekérdezések a várt módon működnek, egyszerűen előléptetheti őket az egyéni riasztási szabályokba egy olyan lehetőség kiválasztásával, amely egy riasztáshoz emeli a munkamenetet.

- **Értesítést kaphat, ha fenyegetések történnek**
    
    Összehasonlíthatja a veszélyforrások adatcsatornáit az összesített naplózási adatokat, és értesítést kaphat, ha egyezés történik. A veszélyforrások adatcsatornái folyamatos adatstreamek, amelyek potenciális vagy aktuális fenyegetésekkel kapcsolatosak, így előfordulhat, hogy az értesítés potenciális fenyegetést jelez a szervezet számára. Hozzon létre egy livestream-munkamenetet egyéni riasztási szabály helyett, ha értesítést szeretne kapni egy esetleges probléma alól anélkül, hogy egy egyéni riasztási szabályt kellene fenntartania.

- **Vizsgálatok indítása**
    
    Ha van olyan aktív vizsgálat, amely magában foglal egy eszközt, például egy gazdagépet vagy egy felhasználót, megtekintheti az adott eszközön előforduló konkrét (vagy bármely) tevékenységet a naplóban. Értesítést kaphat a tevékenység bekövetkezésekor.


## <a name="create-a-livestream-session"></a>Livestream-munkamenet létrehozása

Létrehozhat egy livestream-munkamenetet egy meglévő vadászati lekérdezésből, vagy akár teljesen is létrehozhatja a munkamenetet.

1. A Azure Portal navigáljon a **Sentinel** > **Threat Management** > **vadászathoz**.

2. Livestream-munkamenet létrehozása egy vadászati lekérdezésből:
    
    1. A **lekérdezések** lapon keresse meg a használni kívánt vadászati lekérdezést.
    2. Kattintson a jobb gombbal a lekérdezésre, és válassza a **Hozzáadás a livestream-hoz**lehetőséget. Például:
    
    > [!div class="mx-imgBorder"]
    > ![Livestream-munkamenet létrehozása az Azure Sentinel vadászati lekérdezésből](./media/livestream/livestream-from-query.png)

3. Livestream-munkamenet létrehozása a semmiből: 
    
    1. A **livestream** lap kiválasztása
    2. Válassza **az Ugrás a livestream-hoz**lehetőséget.
    
4. A **livestream** panelen:
    
    - Ha egy lekérdezésből indította el a livestream-t, tekintse át a lekérdezést, és végezze el a kívánt módosításokat.
    - Ha elindította a livestream-t a semmiből, hozza létre a lekérdezést. 

5. Válassza a **Lejátszás** elemet a parancssorból.
    
    A parancssáv alatti állapotsor jelzi, hogy a livestream-munkamenet fut vagy szüneteltetve van. A következő példában a munkamenet fut:
    
    > [!div class="mx-imgBorder"]
    > ![Livestream-munkamenet létrehozása az Azure Sentinel-vadászattal](./media/livestream/livestream-session.png)

6. Válassza a **Mentés** elemet a parancssorból.
    
    Ha nem választja a **szüneteltetés**lehetőséget, a munkamenet addig fut, amíg ki nem jelentkezik a Azure Portalból.

## <a name="view-your-livestream-sessions"></a>A livestream-munkamenetek megtekintése

1. A Azure Portal navigáljon a **Sentinel** > **Threat Management** > **Hunt** > **livestream** lapra.

2. Válassza ki a megtekinteni vagy szerkeszteni kívánt livestream-munkamenetet. Például:
    
    > [!div class="mx-imgBorder"]
    > ![Livestream-munkamenet létrehozása az Azure Sentinel vadászati lekérdezésből](./media/livestream/livestream-tab.png)
    
    Ekkor megnyílik a kiválasztott livestream-munkamenet a lejátszás, a szüneteltetés, a Szerkesztés és így tovább.

## <a name="receive-notifications-when-new-events-occur"></a>Értesítések fogadása új események bekövetkezésekor

Mivel az új eseményekhez tartozó livestream-értesítések Azure Portal értesítéseket használnak, a Azure Portal használatakor ezek az értesítések jelennek meg. Például:

![Azure Portal értesítés a livestream-ról](./media/livestream/notification.png)

Válassza ki az értesítést a **livestream** panel megnyitásához.
 
## <a name="elevate-a-livestream-session-to-an-alert"></a>A livestream-munkamenetek megemelése egy riasztáshoz

A livestream-munkamenet egy új riasztáshoz való előléptetéséhez válassza a **jogosultságszint emelése riasztásra** lehetőséget a megfelelő livestream-munkamenetben a parancssáv:

> [!div class="mx-imgBorder"]
> ![A livestream-munkamenet emelése riasztáshoz](./media/livestream/elevate-to-alert.png)

Ez a művelet megnyitja a szabály létrehozása varázslót, amely előre fel van töltve a livestream-munkamenethez társított lekérdezéssel.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan használhatja a Hunting livestream-t az Azure Sentinelben. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- [Proaktív vadászat a fenyegetések ellen](hunting.md)
- [Automatikus vadászati kampányok futtatása jegyzetfüzetek használatával](notebooks.md)
