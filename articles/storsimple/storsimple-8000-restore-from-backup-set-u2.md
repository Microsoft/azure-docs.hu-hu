---
title: Kötet visszaállítása a biztonsági másolatból egy StorSimple 8000 sorozaton | Microsoft Docs
description: A cikk azt ismerteti, hogy a StorSimple Eszközkezelő szolgáltatás biztonsági mentési katalógusával hogyan állíthatja vissza a StorSimple kötetét egy biztonságimásolat-készletből.
services: storsimple
documentationcenter: NA
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.date: 07/15/2020
ms.author: alkohli
ms.openlocfilehash: ff70df225b5ed27960c96889d409d7005f0fbcc4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94950730"
---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>StorSimple-kötet helyreállítása biztonságimásolat-készletből

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag a StorSimple 8000 Series-eszközön végrehajtott visszaállítási műveletet ismerteti egy meglévő biztonságimásolat-készlet használatával. A **biztonsági mentési katalógus** panel használatával állíthatja vissza a kötetet egy helyi vagy Felhőbeli biztonsági másolatból. A **biztonságimásolat-katalógus** panel megjeleníti a manuális vagy automatikus biztonsági mentések során létrehozott összes biztonságimásolat-készletet. A biztonságimásolat-készlet visszaállítási művelete azonnal online állapotba helyezi a kötetet, miközben az adatok a háttérben letöltődnek.

## <a name="before-you-restore"></a>A visszaállítás előtt

A visszaállítás megkezdése előtt tekintse át a következő figyelmeztetéseket:

* A visszaállítási művelet megkezdése előtt a **kötetet offline állapotba kell állítania** . a kötetet a gazdagépen és az eszközön is offline állapotban kell tartani. Bár a visszaállítási művelet automatikusan online állapotba helyezi a kötetet az eszközön, manuálisan kell az eszközt online állapotba helyezni a gazdagépen. A kötetet online állapotba helyezheti a gazdagépen, amint a kötet online állapotban van az eszközön. (Nem kell megvárnia, amíg a visszaállítási művelet be nem fejeződik.) Az eljárásokhoz lépjen a [kötet offline állapotba](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline)állítása elemre.

* **Kötet típusa visszaállítás után** – a törölt kötetek a pillanatkép típusa alapján lesznek visszaállítva. vagyis a helyileg rögzített kötetek a helyileg rögzített kötetekként lesznek visszaállítva, és a rendszer rétegű kötetekként állítja vissza a köteteket.

    Meglévő kötetek esetében a kötet aktuális használati típusa felülbírálja a pillanatképben tárolt típust. Ha például egy kötetet egy olyan pillanatképből állít vissza, amely a kötet típusának meghatározásakor lett elvégezve, és a kötet típusa már helyileg rögzített (egy végrehajtott átalakítási művelet miatt), akkor a kötet helyileg rögzített kötetként lesz visszaállítva. Hasonlóképpen, ha egy meglévő, helyileg rögzített kötet ki lett bontva, és később vissza lett állítva egy korábbi pillanatképből, amelyet a kötet kisebb volt, akkor a visszaállított kötet megőrzi a jelenlegi kibontott méretet.

    A köteteket nem konvertálhatja többrétegű kötetről helyileg rögzített kötetre, illetve helyileg rögzített kötetről egy többkötetes kötetre a kötet visszaállítása közben. Várjon, amíg a visszaállítási művelet befejeződik, majd alakítsa át a kötetet más típusra. A kötetek konvertálásával kapcsolatos információkért lépjen [a kötet típusának módosítása](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)elemre. 

* **A kötet mérete tükröződik a visszaállított kötetben** – ez fontos szempont, ha olyan helyileg rögzített kötetet állít vissza, amely törölve lett (mivel a helyileg rögzített kötetek teljes mértékben kiépítve vannak). Győződjön meg arról, hogy elegendő lemezterülettel rendelkezik, mielőtt megkísérli visszaállítani a korábban törölt helyileg rögzített kötetet.

* A **kötetet nem lehet kibontani** a visszaállítás közben – várjon, amíg a visszaállítási művelet befejeződik, mielőtt megpróbálja kibővíteni a kötetet. A kötetek kibővítésével kapcsolatos információkért lépjen a [Kötet módosítása](storsimple-8000-manage-volumes-u2.md#modify-a-volume)elemre.

* **Helyi kötet visszaállításakor biztonsági mentést végezhet** – az eljárásokért lépjen [a StorSimple Eszközkezelő szolgáltatás használatára a biztonsági mentési házirendek kezeléséhez](storsimple-8000-manage-backup-policies-u2.md).

* **Visszaállíthat egy visszaállítási műveletet** – Ha megszakítja a visszaállítási feladatot, a kötet visszakerül a visszaállítási művelet elindítása előtt megadott állapotba. Az eljárásokért lépjen a [feladat megszakítása](storsimple-8000-manage-jobs-u2.md#cancel-a-job)elemre.

## <a name="how-does-restore-work"></a>A visszaállítási művelet működése

A 4-es vagy újabb frissítést futtató eszközökön hő-alapú visszaállítást kell megvalósítani. Ahogy a gazdagép az adatelérési kérelmeket eléri az eszközön, a rendszer nyomon követi a kérelmeket, és létrehoz egy hő. A magas kérelmek gyakorisága nagyobb mennyiségű adattömböket eredményez, míg az alacsonyabb kérelmek aránya az alacsonyabb hőmérsékletű adattömbökre van lefordítva. Legalább kétszer hozzá kell férnie az adatokhoz _._ Egy módosított fájl is láthatóként van megjelölve _._ Miután elindította a visszaállítást, az adatkezelési folyamat az hő alapján történik. A 4-es frissítésnél korábbi verziók esetében az adatletöltés a visszaállítás során csak a hozzáférés alapján történik meg.

A következő kikötések vonatkoznak a hő-alapú visszaállításokra:

* A hő követése csak a lépcsőzetes kötetek esetében engedélyezett, és a helyileg rögzített kötetek nem támogatottak.

* A hő-alapú visszaállítás nem támogatott a kötetek másik eszközre történő klónozásakor. 

* Ha helyben történő visszaállítást végez, és a kötet visszaállítására szolgáló helyi pillanatkép létezik az eszközön, akkor nem történik meg a rehidratálás (mivel az adatok már helyileg is elérhetők). 

* Alapértelmezés szerint a visszaállításkor a rendszer kezdeményezi a rehidratáló feladatokat, amelyek proaktív módon rehidratálják az adatokon alapuló hő. 

A 4. frissítésben a Windows PowerShell-parancsmagok segítségével lekérdezheti a futó rehidratáló feladatokat, megszakíthatja a rehidratálás feladatát, és lekérheti a rehidratáló feladat állapotát.

* `Get-HcsRehydrationJob` – Ez a parancsmag beolvassa a rehidratáló feladatok állapotát. Egyetlen újrahidratáló feladatot indítunk el egy köteten.

* `Set-HcsRehydrationJob` – Ez a parancsmag lehetővé teszi a rehidratáló feladat szüneteltetését, leállítását, folytatását, ha a rehidratálás folyamatban van.

A rehidratáló parancsmagokkal kapcsolatos további információkért nyissa meg a [Windows PowerShell-parancsmagok referenciáját a StorSimple](/powershell/module/hcs/?viewFallbackFrom=winserverr2-ps).

Automatikus rehidratálás esetén általában nagyobb átmeneti olvasási teljesítmény várható. A tökéletesítések tényleges nagysága számos tényezőtől függ, például a hozzáférési mintával, az adatváltozással és az adattípussal. 

A rehidratáló feladat megszakításához használhatja a PowerShell-parancsmagot. Ha végleg le szeretné tiltani a rehidratálás feladatait az összes jövőbeli visszaállításhoz, [forduljon a Microsoft ügyfélszolgálatahoz](storsimple-8000-contact-microsoft-support.md).

## <a name="how-to-use-the-backup-catalog"></a>A biztonsági mentési katalógus használata

A **biztonságimásolat-katalógus** panel egy olyan lekérdezést biztosít, amely segít a biztonságimásolat-készlet kijelölésének szűkítéséhez. A következő paraméterek alapján szűrheti a beolvasott biztonságimásolat-készleteket:

* **Time Range (időtartomány** ) – a biztonságimásolat-készlet létrehozásakor beállított dátum és időtartomány.
* **Eszköz** – az eszköz, amelyen a biztonságimásolat-készlet létrejött.
* **Biztonsági mentési házirend** vagy **kötet** – a biztonságimásolat-készlethez társított biztonsági mentési szabályzat vagy kötet.

A szűrt biztonságimásolat-készletek a következő attribútumok alapján lesznek táblázatos formában:

* **Name (név** ) – a biztonságimásolat-készlethez társított biztonsági mentési szabályzat vagy kötet neve.
* **Típus** – a biztonságimásolat-készletek helyi Pillanatképek vagy Felhőbeli Pillanatképek lehetnek. A helyi pillanatkép az eszközön helyileg tárolt összes kötet-adatok biztonsági mentése, míg a Felhőbeli pillanatkép a felhőben található kötet-adatok biztonsági mentésére utal. A helyi Pillanatképek gyorsabb hozzáférést biztosítanak, míg a Felhőbeli Pillanatképek az adatrugalmasság érdekében vannak kiválasztva.
* **Size (méret** ) – a biztonságimásolat-készlet tényleges mérete.
* **Létrehozva** – a biztonsági másolatok létrehozásának dátuma és időpontja. 
* **Kötetek** – a biztonságimásolat-készlethez társított kötetek száma.
* **Kezdeményezve** – a biztonsági mentések ütemezése vagy manuálisan, egy felhasználó alapján automatikusan is elindíthatók. (A biztonsági mentések ütemezéséhez használhat biztonsági mentési szabályzatot is. Azt is megteheti, hogy a **biztonsági mentés** lehetőség használatával interaktív vagy igény szerinti biztonsági mentést készít.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>A StorSimple kötet visszaállítása biztonsági másolatból

A **biztonsági mentési katalógus** panel használatával visszaállíthatja a StorSimple kötetet egy adott biztonsági másolatból. Ne feledje azonban, hogy egy kötet visszaállítása visszaállítja a kötetet a biztonsági mentés során megjelenő állapotba. A biztonsági mentési művelet után hozzáadott összes adattal elvész a rendszer.

> [!WARNING]
> A biztonsági másolatból történő visszaállításkor a rendszer lecseréli a meglévő köteteket a biztonsági másolatból. Ennek hatására előfordulhat, hogy a biztonsági mentés után írt összes adatvesztés megszakad.


### <a name="to-restore-your-volume"></a>A kötet visszaállítása
1. Lépjen a StorSimple Eszközkezelő szolgáltatásra, majd kattintson a **biztonsági mentési katalógus** lehetőségre.

2. Válasszon egy biztonságimásolat-készletet a következők szerint:
   
   1. Határozza meg az időtartományt.
   2. Válassza ki a megfelelő eszközt.
   3. A legördülő listában válassza ki a kijelölni kívánt biztonsági másolathoz tartozó kötetet vagy biztonsági mentési szabályzatot.
   4. A lekérdezés végrehajtásához kattintson az **alkalmaz** gombra.

      A kiválasztott kötethez vagy biztonsági mentési szabályzathoz társított biztonsági másolatoknak szerepelniük kell a biztonságimásolat-készletek listájában.
   
      ![Biztonságimásolat-készlet listája](./media/storsimple-8000-restore-from-backup-set-u2/bucatalog.png)     
     
3. Bontsa ki a biztonságimásolat-készletet a társított kötetek megtekintéséhez. Ezeket a köteteket offline állapotba kell helyezni a gazdagépen és az eszközön, mielőtt azok visszaállíthatók. Nyissa meg a köteteket az eszköz **kötetek** paneljén, majd kövesse a kötetek [Offline](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline) állapotba állítása című témakör lépéseit.
   
   > [!IMPORTANT]
   > Először is győződjön meg arról, hogy a köteteket offline állapotba helyezte a gazdagépen, mielőtt a kötetek offline állapotba kerültek az eszközön. Ha nem végzi el a kötetek offline állapotba helyezését a gazdagépen, lehetséges, hogy adatsérülést eredményezhet.
   
4. Váltson vissza a **biztonsági mentési katalógus** lapra, és válassza ki a biztonságimásolat-készletet. Kattintson a jobb gombbal, majd a helyi menüben válassza a **visszaállítás** lehetőséget.

    ![Biztonságimásolat-készlet 2. listája](./media/storsimple-8000-restore-from-backup-set-u2/restorebu1.png)

5. A rendszer a művelet megerősítését kéri. Tekintse át a visszaállítási információkat, majd jelölje be a megerősítő jelölőnégyzetet.
   
    ![Megerősítő oldal](./media/storsimple-8000-restore-from-backup-set-u2/restorebu2.png)

7. Kattintson a **visszaállítás** gombra. Ez egy visszaállítási feladatot kezdeményez, amelyet a **feladatok** lap elérésével tekinthet meg.

   ![2. megerősítő lap](./media/storsimple-8000-restore-from-backup-set-u2/restorebu5.png)

8. A visszaállítás befejeződése után ellenőrizze, hogy a kötetek tartalmát a biztonsági másolatból kötetekre cseréli-e a rendszer.


## <a name="if-the-restore-fails"></a>Ha a visszaállítás sikertelen

A rendszer riasztást küld, ha a visszaállítási művelet valamilyen okból meghiúsul. Ha ez történik, frissítse a biztonsági mentési listát annak ellenőrzéséhez, hogy a biztonsági másolat még érvényes-e. Ha a biztonsági mentés érvényes, és a felhőből állítja vissza, a kapcsolódási problémák okozhatják a problémát.

A visszaállítási művelet befejezéséhez állítsa offline állapotba a kötetet a gazdagépen, majd próbálja megismételni a visszaállítási műveletet. Vegye figyelembe, hogy a visszaállítási folyamat során végrehajtott összes módosítás elvész.

## <a name="next-steps"></a>Következő lépések
* Ismerje meg, hogyan [kezelheti a StorSimple-köteteket](storsimple-8000-manage-volumes-u2.md).
* Ismerje meg, hogyan kezelheti a [StorSimple-eszközt a StorSimple Eszközkezelő szolgáltatással](storsimple-8000-manager-service-administration.md).