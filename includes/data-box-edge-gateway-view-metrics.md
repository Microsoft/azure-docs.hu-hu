---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 9ac7966538102273b91d6b7f15b90e18ceedd421
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779938"
---
A mérőszámokat megtekintheti az eszköz teljesítményének figyeléséhez, illetve egyes példányokban az eszközök problémáinak elhárításához.

Hozzon létre egy diagramot a kiválasztott eszköz metrikáinak létrehozásához a Azure Portal alábbi lépésein.

1. A Azure Portal erőforrásához lépjen a **figyelés > metrikák** elemre, és válassza a **metrika hozzáadása**elemet.

    ![Metrika hozzáadása](media/data-box-edge-gateway-view-metrics/view-metrics-1.png)

2. Az erőforrás automatikusan ki van töltve.  

    ![Aktuális erőforrás](media/data-box-edge-gateway-view-metrics/view-metrics-2.png)

    Egy másik erőforrás megadásához válassza ki az erőforrást. Az **erőforrás kiválasztása** panelen válassza ki az előfizetést, az erőforráscsoportot, az erőforrás típusát és az adott erőforrást, amelynek a metrikáit meg szeretné jeleníteni, majd válassza az **alkalmaz**lehetőséget.

    ![Válasszon másik erőforrást](media/data-box-edge-gateway-view-metrics/view-metrics-3.png)

3. A legördülő listából válassza ki az eszköz figyelésére szolgáló mérőszámot. A metrikák teljes listáját itt tekintheti meg: [mérőszámok az eszközön](#metrics-on-your-device).

4. Ha ki van választva egy metrika a legördülő listából, az Összesítés is meghatározható. Az Összesítés a megadott időtartamon keresztül összesített tényleges értékre hivatkozik. Az összesített értékek lehet átlagos, minimum vagy a maximális érték. Válassza ki az összesítést az AVG, a Max vagy a min értékből.

    ![Diagram megtekintése](media/data-box-edge-gateway-view-metrics/view-metrics-4.png)

5. Ha a kiválasztott metrika több példánnyal rendelkezik, akkor a felosztási lehetőség elérhető. Válassza a **felosztás alkalmazása** lehetőséget, majd válassza ki azt az értéket, amelynek meg szeretné tekinteni a részletezését.

    ![Felosztás alkalmazása](media/data-box-edge-gateway-view-metrics/view-metrics-5.png)

6. Ha most már csak néhány példány esetében szeretné megtekinteni a részletezést, szűrheti az adatszűrést. Például ebben az esetben, ha a hálózati átviteli sebességet csak az eszköz két csatlakoztatott hálózati adapterén szeretné látni, akkor szűrheti ezeket a csatolókat. Válassza a **szűrő hozzáadása** lehetőséget, és adja meg a hálózati adapter nevét a szűréshez.

    ![Szűrő hozzáadása](media/data-box-edge-gateway-view-metrics/view-metrics-6.png)

7. Az egyszerű hozzáférés érdekében a diagramot is rögzítheti az irányítópulton.

    ![Rögzítés az irányítópulton](media/data-box-edge-gateway-view-metrics/view-metrics-7.png)

8. A diagram adatainak Excel-számolótáblába való exportálásához, vagy a megosztható diagramra mutató hivatkozás beszerzéséhez kattintson a megosztás lehetőségre a parancssáv használatával.

    ![Adatok exportálása](media/data-box-edge-gateway-view-metrics/view-metrics-8.png)
