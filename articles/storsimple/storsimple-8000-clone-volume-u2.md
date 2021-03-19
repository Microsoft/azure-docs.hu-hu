---
title: Kötet klónozása a StorSimple 8000 sorozaton | Microsoft Docs
description: Ismerteti a klónok különböző típusait és használatát, valamint ismerteti, hogyan használhat egy biztonságimásolat-készletet egy StorSimple 8000 Series-eszközön lévő egyes kötetek klónozásához.
services: storsimple
documentationcenter: NA
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.date: 07/15/2020
ms.author: alkohli
ms.openlocfilehash: 5ebb7f1d9440bc7a8b75afe64403762532213c1d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91767579"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-clone-a-volume"></a>Kötet klónozása az Azure Portal StorSimple-eszközkezelő szolgáltatásával

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag leírja, hogyan használhat egy biztonságimásolat-készletet egy adott kötet klónozásához a **biztonsági mentési katalógus** paneljén keresztül. Emellett az *átmeneti* és az *állandó* klónok közötti különbséget is ismerteti. Az oktatóanyagban szereplő útmutatás a 3. vagy újabb verziót futtató összes StorSimple 8000 Series-eszközre vonatkozik.

A StorSimple Eszközkezelő szolgáltatás **biztonságimásolat-katalógusa** panel megjeleníti a manuális vagy automatikus biztonsági mentések során létrehozott összes biztonságimásolat-készletet. Ezt követően kiválaszthat egy kötetet a klónozott biztonságimásolat-készletben.

 ![Biztonságimásolat-készlet listája](./media/storsimple-8000-clone-volume-u2/bucatalog.png)

## <a name="considerations-for-cloning-a-volume"></a>Kötet klónozásának szempontjai

A kötetek klónozásakor vegye figyelembe a következő információkat:

- A klónok ugyanúgy viselkednek, mint a normál kötetek. A klónozáshoz minden lehetséges művelet elérhető.

- A figyelés és az alapértelmezett biztonsági mentés automatikusan le van tiltva a klónozott köteteken. Minden biztonsági mentéshez konfigurálnia kell egy klónozott kötetet.

- Egy helyileg rögzített kötet klónozott kötetként van klónozott. Ha a klónozott kötetnek helyileg rögzítettnek kell lennie, a klónozást egy helyileg rögzített kötetre konvertálhatja a klónozási művelet sikeres befejeződése után. A többkötetes kötetek helyileg rögzített kötetre való konvertálásával kapcsolatos információkért lépjen [a kötet típusának módosítása](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)elemre.

- Ha a klónozott kötetet a klónozást követően közvetlenül a klónozás után (ha még mindig átmeneti klón) próbálják alakítani, a konverzió a következő hibaüzenettel meghiúsul:

    `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.`

    A rendszer csak akkor fogadja ezt a hibát, ha egy másik eszközre klónozást végez. A kötetet sikeresen átalakíthatja helyileg rögzítettre, ha először konvertálja az átmeneti klónt egy állandó klónba. Készítsen egy Felhőbeli pillanatképet az átmeneti klónról, és alakítsa át állandó klónozásra.

## <a name="create-a-clone-of-a-volume"></a>Kötet klónozásának létrehozása

Létrehozhat egy klónt ugyanazon az eszközön, egy másik eszközön, vagy akár egy felhőalapú készüléken is egy helyi vagy Felhőbeli pillanatkép használatával.

Az alábbi eljárás azt ismerteti, hogyan hozhat létre egy klónt a biztonsági mentési katalógusból.  

A következő lépések végrehajtásával hozza létre a kötet klónját a biztonsági mentési katalógusból.

#### <a name="to-clone-a-volume"></a>Kötet klónozása

1. Lépjen a StorSimple Eszközkezelő szolgáltatásra, majd kattintson a **biztonsági mentési katalógus** lehetőségre.

2. Válasszon egy biztonságimásolat-készletet a következők szerint:
   
   1. Válassza ki a megfelelő eszközt.
   2. A legördülő listában válassza ki a kijelölni kívánt biztonsági másolathoz tartozó kötetet vagy biztonsági mentési szabályzatot.
   3. Határozza meg az időtartományt.
   4. A lekérdezés végrehajtásához kattintson az **alkalmaz** gombra.

      A kiválasztott kötethez vagy biztonsági mentési szabályzathoz társított biztonsági másolatoknak szerepelniük kell a biztonságimásolat-készletek listájában.
   
      ![Biztonságimásolat-készlet 2. listája](./media/storsimple-8000-clone-volume-u2/bucatalog.png)
     
3. Bontsa ki a biztonságimásolat-készletet, hogy megtekintse a társított kötetet, és válasszon egy kötetet egy biztonságimásolat-készletben. Kattintson a jobb gombbal, majd a helyi menüben válassza a **klónozás** lehetőséget.

   ![Biztonságimásolat-készlet 3. listája](./media/storsimple-8000-clone-volume-u2/clonevol3b.png) 

3. A **klónozás** panelen hajtsa végre a következő lépéseket:
   
   1. Jelölje ki a kívánt eszközt. Ez az a hely, ahol a klón létre lesz hozva. Kiválaszthatja ugyanazt az eszközt, vagy megadhat egy másik eszközt is.

      > [!NOTE]
      > Győződjön meg arról, hogy a klónozáshoz szükséges kapacitás alacsonyabb, mint a megcélzott eszközön rendelkezésre álló kapacitás.
       
   2. Adja meg a klón egyedi kötetének nevét. A névnek 3 és 127 karakter közöttinek kell lennie.
      
       > [!NOTE]
       > A **klónozott kötet as** mezője akkor is fel lesz **bontva** , ha egy helyileg rögzített kötetet klónozást végez. Ez a beállítás nem módosítható; Ha azonban szükség van a klónozott kötet helyi rögzítésére is, akkor a klón sikeres létrehozása után konvertálhatja a klónozást egy helyileg rögzített kötetre. A többkötetes kötetek helyileg rögzített kötetre való konvertálásával kapcsolatos információkért lépjen [a kötet típusának módosítása](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)elemre.
          
   3. A **csatlakoztatott gazdagépek** területen határozzon meg egy hozzáférés-vezérlési REKORDOT (ACR) a klónozáshoz. Hozzáadhat egy új ACR-t, vagy választhat a meglévő listából is. Az ACR meghatározza, hogy mely gazdagépek férhetnek hozzá ehhez a klónhoz.
      
       ![Biztonságimásolat-készlet listája 4](./media/storsimple-8000-clone-volume-u2/clonevol3a.png) 

   4. A művelet befejezéséhez kattintson a **klónozás** gombra.

4. A klónozott feladatok elindulnak, és értesítést kap, amikor a klón sikeresen létrejött. A klónozási feladat figyeléséhez kattintson a feladat értesítése vagy a **feladatok** panelre.

    ![Biztonságimásolat-készlet 5. listája](./media/storsimple-8000-clone-volume-u2/clonevol5.png)

7. A klónozási feladatok befejezése után lépjen az eszközre, majd kattintson a **kötetek** elemre. A kötetek listájában látnia kell a klónozást, amelyet az imént hozott létre ugyanabban a kötet-tárolóban, amelyben a forrás kötet található.

    ![Biztonságimásolat-készlet listája 6](./media/storsimple-8000-clone-volume-u2/clonevol6.png)

Az így létrehozott klón egy átmeneti klón. További információ a klónozási típusokról: [átmeneti és állandó klónok](#transient-vs-permanent-clones).


## <a name="transient-vs-permanent-clones"></a>Átmeneti és állandó klónok
Az átmeneti klónok csak akkor jönnek létre, amikor egy másik eszközre klónozást végez. A biztonságimásolat-készletből egy adott kötetet egy másik, a StorSimple Eszközkezelő által felügyelt eszközre is klónozott. Az átmeneti klón az eredeti köteten lévő adatokra hivatkozik, és ezeket az adatokkal helyileg olvashatja és írhatja a céleszköz.

Miután elvégezte a Felhőbeli pillanatképet egy átmeneti klónról, az eredményül kapott klón egy *állandó* klón. A folyamat során a rendszer létrehozza az adatmásolatot a felhőben, és az Adatmásolás idejét az adatmennyiség és az Azure-késés (ez egy Azure – Azure-példány) alapján határozza meg. Ez a folyamat több napot is igénybe vehet. Az átmeneti klón állandó klón lesz, és nem rendelkezik az eredeti kötet azon adatainak hivatkozásával, amelyekről klónozott volt.

## <a name="scenarios-for-transient-and-permanent-clones"></a>Átmeneti és állandó klónok forgatókönyvei
Az alábbi szakaszok olyan példákat ismertetnek, amelyekben átmeneti és állandó klónozást lehet használni.

### <a name="item-level-recovery-with-a-transient-clone"></a>Elemszintű helyreállítás átmeneti klónozással
Egy egyéves Microsoft PowerPoint-bemutató fájlt kell helyreállítani. A rendszergazda azonosítja az adott időpontra vonatkozó biztonsági mentést, majd szűri a kötetet. A rendszergazda ezután klónozott a kötetet, megkeresi a keresett fájlt, és megadja Önnek. Ebben a forgatókönyvben egy átmeneti klónt használunk.

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Éles környezetben végzett tesztelés állandó klónozással
Ellenőrizze, hogy van-e tesztelési hiba az éles környezetben. Létre kell hoznia egy klónt a kötetről az éles környezetben, majd el kell készítenie egy Felhőbeli pillanatképet a klónról egy független klónozott kötet létrehozásához. Ebben az esetben állandó klónozást használunk.

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [állíthatja vissza a StorSimple kötetét egy biztonságimásolat-készletből](storsimple-8000-restore-from-backup-set-u2.md).
* Ismerje meg, hogyan kezelheti a [StorSimple-eszközt a StorSimple Eszközkezelő szolgáltatással](storsimple-8000-manager-service-administration.md).

