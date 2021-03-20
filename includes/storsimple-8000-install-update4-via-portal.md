---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: c3cfab78ab4bcc92b3e65b5e4fa04a29bf688de0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93376277"
---
#### <a name="to-install-an-update-from-the-azure-portal"></a>Frissítés telepítése az Azure Portalról

1. A StorSimple szolgáltatás oldalán válassza ki az eszközét.

    ![Eszköz kiválasztása](./media/storsimple-8000-install-update4-via-portal/update1.png)

2. Navigáljon az **eszközbeállítások**  >  **eszköz frissítései** menüpontra.

    ![Kattintson az eszközök frissítései lehetőségre](./media/storsimple-8000-install-update4-via-portal/update2.png)

2. Megjelenik egy értesítés, ha új frissítések érhetők el. Azt is megteheti, hogy az **eszköz frissítései** panelen a **frissítések keresése** lehetőségre kattint. Létrejön egy feladat, amely megkeresi az elérhető frissítéseket. A feladat sikeres befejezéséről értesítést kap.

    ![Kattintson az eszköz frissítései 2](./media/storsimple-8000-install-update4-via-portal/update3.png)

3. Azt javasoljuk, hogy mielőtt alkalmazná a frissítést az eszközön, tekintse át a kibocsátási megjegyzéseket. A frissítések alkalmazásához kattintson a **frissítések telepítése** elemre. A frissítések alkalmazása előtt tekintse át az előfeltételeket a **rendszeres frissítések megerősítése** panelen. Jelölje be a jelölőnégyzetet annak jelzéséhez, hogy készen áll az eszköz frissítésére, majd kattintson a **telepítés** gombra.

    ![Kattintson az eszköz frissítései 3](./media/storsimple-8000-install-update4-via-portal/update4.png)

6. Megkezdődik az előfeltételek ellenőrzése. Ezekbe az ellenőrzésekbe a következők tartoznak:
   
   * **A vezérlő állapotának ellenőrzései** annak ellenőrzéséhez, hogy mindkét eszközvezérlő kifogástalan állapotú és online legyen.
   * **A hardverösszetevők állapotának ellenőrzései** annak ellenőrzéséhez, hogy a StorSimple eszközön lévő összes hardverösszetevő kifogástalan állapotú legyen.
   * **DATA 0 ellenőrzések** annak ellenőrzéséhez, hogy a DATA 0 engedélyezett legyen az eszközön. Ha ez az illesztőfelület nem engedélyezett, engedélyeznie kell, majd újra kell próbálkoznia.

     A frissítés letöltése és telepítése csak akkor történik meg, ha az összes ellenőrzés sikeresen befejeződött. Értesítést kap, amikor az ellenőrzések folyamatban vannak. Ha az előzetes ellenőrzések sikertelenek, akkor a hiba okai lesznek elérhetők. Oldja meg ezeket a problémákat, majd próbálja megismételni a műveletet. Elképzelhető, hogy kapcsolatba kell lépnie a Microsoft támogatási szolgálatával, ha nem tudja egyedül kezelni ezeket a problémákat.

7. Az előzetes ellenőrzések sikeres befejeződése után létrejön egy frissítési feladatok. A frissítési feladat sikeres létrehozásáról értesítést kap.
   
    ![Frissítési feladat létrehozása](./media/storsimple-8000-install-update4-via-portal/update6.png)
   
    A rendszer ezután alkalmazza a frissítést az eszközre.

9. A frissítés elvégzése néhány órát igényel. Válassza ki a frissítési feladatot, és kattintson a **Részletek** gombra, így bármikor megtekintheti a feladat részleteit.

    ![A 2. feladatokhoz tartozó frissítés frissítése](./media/storsimple-8000-install-update4-via-portal/update8.png)

     Nyomon követheti a frissítési feladat előrehaladását az **eszközbeállítások > feladatokból** is. A **feladatok** panelen láthatja a frissítés folyamatát.

     ![3. feladatok létrehozásának frissítése](./media/storsimple-8000-install-update4-via-portal/update7.png)

10. A feladatok befejezése után navigáljon az eszközbeállítások **> eszköz frissítései** elemre. A szoftver verzióját most frissíteni kell.

   ![4. frissítési feladatok létrehozása](./media/storsimple-8000-install-update4-via-portal/update9.png)

