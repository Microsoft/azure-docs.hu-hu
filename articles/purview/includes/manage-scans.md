---
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 1/20/2021
ms.openlocfilehash: ac83f455d2af82c3f6970077fccb17f07e53cccc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "98900986"
---
## <a name="creating-and-running-a-scan"></a>Vizsgálat létrehozása és futtatása

> [!Note] 
> Az alábbi lépések és képernyőképek azt mutatják be, hogyan kezelhetők a vizsgálatok a különböző adatforrás-típusok között. A lehetőségek némileg eltérőek lehetnek attól függően, hogy milyen típusú adatforrásokat használt.

Új vizsgálat létrehozásához és futtatásához tegye a következőket:

1. Navigáljon a **forrásokhoz**

1. Válassza ki a regisztrált adatforrást.

1. Válassza az **+ új vizsgálat** lehetőséget

1. Válassza ki az adatforráshoz való kapcsolódáshoz szükséges hitelesítő adatokat. 

   :::image type="content" source="media/manage-scans/set-up-scan.png" alt-text="Vizsgálat beállítása":::

1. A vizsgálat hatókörét az adatforrás adott részeire (például mappák, gyűjtemények vagy sémák) szűkítheti a lista megfelelő elemeinek ellenőrzésével.

   :::image type="content" source="media/manage-scans/scope-your-scan.png" alt-text="A vizsgálat hatóköre":::

1. Válassza a vizsgálathoz beállított vizsgálati szabályt. Választhat, hogy a rendszer alapértelmezett értéke, a meglévő egyéni beállítások vagy egy új beágyazott elem van-e létrehozva.

   :::image type="content" source="media/manage-scans/scan-rule-set.png" alt-text="Szabálykészlet ellenőrzése":::

1. Válassza ki a vizsgálati triggert. Beállíthat egy ütemtervet, vagy futtathatja a vizsgálatot egyszer.

   :::image type="content" source="media/manage-scans/trigger-scan.png" alt-text="eseményindító":::

1. Tekintse át a vizsgálatot, és válassza a **Mentés és Futtatás** lehetőséget.

## <a name="viewing-your-scans-and-scan-runs"></a>A vizsgálatok és a vizsgálat futtatásának megtekintése

A meglévő vizsgálatok megtekintéséhez tegye a következőket:

1. Navigáljon a felügyeleti központhoz. Válassza ki az **adatforrásokat** a **források és vizsgálat** szakaszban. 

2. Válassza ki a kívánt adatforrást. Ekkor megjelenik az adatforrás meglévő vizsgálatának listája.

3. Válassza ki azt a vizsgálatot, amelynek eredményeit szeretné megtekinteni.

4. Ezen az oldalon megtekintheti az összes korábbi vizsgálatot, valamint az egyes vizsgálati futtatások metrikáit és állapotát. Emellett azt is megjeleníti, hogy a vizsgálat ütemezett vagy kézi volt-e, hány eszközön alkalmaztak besorolást, hány teljes eszközt észlelt a rendszer, a vizsgálat kezdési és befejezési időpontja, valamint a vizsgálat teljes időtartama.

## <a name="manage-your-scans---edit-delete-or-cancel"></a>Ellenőrzések kezelése – Szerkesztés, törlés vagy megszakítás

A vizsgálat kezeléséhez vagy törléséhez tegye a következőket:

1. Navigáljon a felügyeleti központhoz. Válassza ki az **adatforrások** lehetőséget a **források és vizsgálat szakaszban,** majd válassza ki a kívánt adatforrást.

2. Válassza ki a kezelni kívánt vizsgálatot. A vizsgálat szerkesztéséhez kattintson a **Szerkesztés** lehetőségre.

3. A vizsgálatot a **delete (Törlés**) lehetőség kiválasztásával törölheti. 
