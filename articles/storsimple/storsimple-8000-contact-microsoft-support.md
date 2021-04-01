---
title: Támogatási jegy vagy eset létrehozása StorSimple 8000 sorozathoz
description: Ismerje meg, hogyan naplózhatja a támogatási kérést, és hogyan indíthat el egy támogatási munkamenetet a StorSimple 8000 Series eszközön.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: c288f59b1cb9a85ed4bc978d1c3fba18ba30b572
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017202"
---
# <a name="contact-microsoft-support"></a>Kapcsolatfelvétel a Microsoft ügyfélszolgálatával

A StorSimple Eszközkezelő lehetővé teszi **egy új támogatási kérelem naplózását** a szolgáltatás összegzési paneljén belül. Ha bármilyen probléma merül fel a StorSimple-megoldással kapcsolatban, létrehozhat egy szolgáltatási kérelmet a technikai támogatáshoz. A támogatási szakemberrel folytatott online munkamenetben szükség lehet egy támogatási munkamenet elindítására is a StorSimple-eszközön. Ez a cikk végigvezeti a következő lépéseken:

* Támogatási kérelem létrehozása.
* Támogatási kérelmek életciklusának kezelése a portálon belülről.
* Támogatási munkamenet elindítása a StorSimple-eszköz Windows PowerShell-felületén.

Support request létrehozása előtt tekintse át a [StorSimple 8000 sorozat támogatási SLA-kat és információkat](./storsimple-8000-support-options.md) .

## <a name="create-a-support-request"></a>Támogatási kérelem létrehozása

A [támogatási csomagtól](https://azure.microsoft.com/support/plans/)függően támogatási jegyek hozhatók létre a StorSimple-eszközön, közvetlenül a StorSimple Eszközkezelő szolgáltatás összefoglalási paneljéről. Támogatási kérelem létrehozásához hajtsa végre a következő lépéseket:

#### <a name="to-create-a-support-request"></a>Támogatási kérelem létrehozása

1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. A szolgáltatás összegzése panel beállításainál válassza a **támogatás + hibaelhárítás** szakaszt, majd kattintson az **új támogatási kérelem** elemre.
     
    ![Kapcsolatfelvétel az MS ügyfélszolgálatával az új portálon keresztül](./media/storsimple-8000-contact-microsoft-support/contactsupport1.png)
   
2. Az **új támogatási kérelem** panelen válassza az **alapismeretek** lehetőséget. Az **alapvető beállítások** panelen végezze el a következő lépéseket:
   1. A **probléma típusa** legördülő listában válassza a **technikai** lehetőséget.
   2. Az aktuális **előfizetés**, a **szolgáltatás** típusa és az **erőforrás** (StorSimple Eszközkezelő szolgáltatás) automatikusan ki van választva. 
   3. Válassza ki a **támogatási csomagot** a legördülő listából, ha az előfizetéshez több csomag van társítva. A technikai támogatás engedélyezéséhez fizetős támogatási csomag szükséges.
   4. Kattintson a **Tovább** gombra.

       ![Kapcsolatfelvétel az MS ügyfélszolgálatával az új portálon keresztül 2](./media/storsimple-8000-contact-microsoft-support/contactsupport2.png)

3. Az **új támogatási kérelem** panelen válassza a **2. lépés problémát**. A **probléma** panelen hajtsa végre a következő lépéseket:
    
    1. Válassza ki a **súlyosságot**.
    2. Adja meg, hogy a probléma a készülékhez vagy a StorSimple Eszközkezelő szolgáltatáshoz kapcsolódik-e.
    3. Válassza ki a probléma **kategóriáját** , és adja meg a probléma további **részleteit** .
    4. Adja meg a probléma kezdő dátumát és időpontját.
    5. A **fájlfeltöltés** területen kattintson a mappa ikonra a támogatási csomag megkereséséhez.
    6. A **diagnosztikai adatok megosztásának** megadásához.
    7. Kattintson a **Tovább** gombra.

       ![Kapcsolatfelvétel az MS ügyfélszolgálatával az új portálon keresztül 3](./media/storsimple-8000-contact-microsoft-support/contactsupport3.png) 

4. Az **új támogatási kérelem** panelen kattintson a **3. lépés kapcsolattartási adatok** elemre. A **kapcsolattartási adatok** panelen hajtsa végre a következő lépéseket:

   1. A **kapcsolattartási beállítások** területen adja meg a kívánt kapcsolattartási módszert (telefon vagy e-mail) és a nyelvet. A válaszidő automatikusan ki van választva az előfizetési terv alapján.
   2. A kapcsolattartási adatok területen adja meg a nevét, az e-mail-címet, a nem kötelező kapcsolattartót, az országot/régiót. Jelölje be a **kapcsolattartási változások mentése a későbbi támogatási kérelmekhez** jelölőnégyzetet.
   3. Kattintson a **Létrehozás** lehetőségre.
   
       ![Forduljon az MS ügyfélszolgálatához az új portálon keresztül 4](./media/storsimple-8000-contact-microsoft-support/contactsupport5.png)   

      A Microsoft ügyfélszolgálata ezeket az információkat a további információk, a diagnosztika és a megoldás elérésére fogja használni.
      A kérés elküldése után a támogatási szakember a lehető leghamarabb felveszi Önnel a kapcsolatot, hogy folytassa a kérést.

## <a name="manage-a-support-request"></a>Támogatási kérelem kezelése

A támogatási jegy létrehozása után a jegyet a teljes életciklusán keresztül kezelheti a portálon.

#### <a name="to-manage-your-support-requests"></a>A támogatási kérések kezelése

1. A Súgó és támogatás lapon keresse meg a **tallózás > Súgó + támogatás** menüpontot.

    ![Támogatási kérelmek kezelése](./media/storsimple-8000-contact-microsoft-support/managesupport1.png)

2. Az összes támogatási kérelem táblázatos felsorolása megjelenik a **Súgó + támogatás** panelen.

    ![Támogatási kérelmek kezelése 2](./media/storsimple-8000-contact-microsoft-support/managesupport2.png)

3. Válasszon ki egy támogatási kérést, és kattintson rá. Megtekintheti a kérelem állapotát és részleteit. Ha ezt a kérést szeretné követni, kattintson az **+ új üzenet** elemre.

    ![Támogatási kérelmek kezelése 3](./media/storsimple-8000-contact-microsoft-support/managesupport3.png)

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Támogatási munkamenet elindítása Windows PowerShell StorSimple-bővítménye

A StorSimple eszközzel kapcsolatos problémák elhárításához a Microsoft ügyfélszolgálata csapatot kell használnia. Előfordulhat, hogy a Microsoft ügyfélszolgálata támogatási munkamenetet kell használnia az eszközre való bejelentkezéshez.

A támogatási munkamenet elindításához hajtsa végre a következő lépéseket:

#### <a name="to-start-a-support-session"></a>Támogatási munkamenet elindítása

1. Közvetlenül a soros konzol vagy egy távoli számítógépről származó Telnet-munkamenet használatával férhet hozzá az eszközhöz. Ehhez kövesse a [Putty használata az eszköz soros konzolhoz való kapcsolódáshoz](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)című témakör lépéseit.
2. A megnyíló munkamenetben nyomja le az **ENTER** billentyűt a parancssor beszerzéséhez.
3. A soros konzol menüjében válassza az 1. lehetőséget, majd **Jelentkezzen be a teljes hozzáférés** lehetőséggel.
4. A parancssorba írja be a következő jelszót:
   
    `Password1`
5. A parancssorba írja be a következő parancsot:
   
    `Enable-HcsSupportAccess`
6. Egy titkosított karakterlánc jelenik meg Önnek. Másolja ezt a karakterláncot egy szövegszerkesztőbe, például a Jegyzettömbbe.
7. Mentse ezt a karakterláncot, és küldje el egy e-mail-üzenetben, hogy Microsoft ügyfélszolgálata.

> [!IMPORTANT]
> A támogatás hozzáférését letilthatja a futtatásával `Disable-HcsSupportAccess` . A StorSimple-eszköz a munkamenet elindítása után 8 órával is megkísérli letiltani a támogatás elérését. Az ajánlott eljárás a StorSimple-eszköz hitelesítő adatainak módosítása a támogatási munkamenet elindítása után.


## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [diagnosztizálhatja és megoldhatja a StorSimple 8000 Series eszközével kapcsolatos problémákat](storsimple-8000-troubleshoot-deployment.md)