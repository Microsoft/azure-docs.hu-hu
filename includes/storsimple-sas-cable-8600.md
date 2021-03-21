---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: f08a6b3f7abfc79bff6baff2a339053905612535
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96026302"
---
#### <a name="to-attach-the-sas-cables"></a>SAS-kábelek csatolása
1. Azonosítsa az elsődleges és a EBOD-házakat. A két ház azonosítható a megfelelő hátsó síkok alapján. Útmutatásért tekintse meg az alábbi képet. 
   
    ![Elsődleges és EBOD-házak háttérbeli síkja](./media/storsimple-sas-cable-8600/HCSBackplaneofprimaryandEBODenclosure.png)
   
    **Az elsődleges és a EBOD-házak háttérbeli nézete**
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |Elsődleges ház |
   | 2 |EBOD ház |
2. Keresse meg az elsődleges és a EBOD-házak sorozatszámait. A sorozatszám matrica az egyes házak hátoldalán található. A sorozatszámoknak azonosnak kell lenniük mindkét házban. Ha a sorozatszámok nem egyeznek, [forduljon azonnal Microsoft ügyfélszolgálata](../articles/storsimple/storsimple-8000-contact-microsoft-support.md) . A sorozatszámok megkereséséhez tekintse meg az alábbi ábrát.
   
    ![Sorozatszámot ábrázoló ház hátsó nézete](./media/storsimple-sas-cable-8600/HCSRearviewofenclosureindicatinglocationofserialnumbersticker.png)
   
    **Sorozatszám-matrica helye**
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |A ház füle |
3. A megadott SAS-kábelek használatával a következő módon csatlakoztathatja a EBOD-bekerítést az elsődleges házba:
   
   1. Azonosítsa a négy SAS-portot az elsődleges házban és a EBOD házban. Az SAS-portok az elsődleges házban található EBOD vannak megjelölve, és megfelelnek az a portnak a EBOD-házban, ahogyan az az alábbi SAS-kábelezési ábrán látható.
   2. A megadott SAS-kábelek használatával csatlakoztathatja a EBOD-portot az A porthoz.
   3. A EBOD-portnak a 0. vezérlőn kell csatlakoznia az a porthoz a 0. EBOD-vezérlőn. Az 1. vezérlő EBOD-portjának csatlakoznia kell az A porthoz az 1. EBOD-vezérlőn. Útmutatásért tekintse meg az alábbi ábrát. 
      
      ![SAS-kábelek az eszközhöz](./media/storsimple-sas-cable-8600/HCSSAScablingforyourdevice.png)
      
      **SAS kábelezés**
      
      | Címke | Description |
      |:--- |:--- |
      | A |Elsődleges ház |
      | B |EBOD ház |
      | 1 |Vezérlő 0 |
      | 2 |Vezérlő 1 |
      | 3 |EBOD vezérlő 0 |
      | 4 |1. EBOD-vezérlő |
      | 5, 6 |SAS-portok az elsődleges bekerítésen (címkézett EBOD) |
      | 7, 8 |SAS-portok a EBOD ház (A port) |