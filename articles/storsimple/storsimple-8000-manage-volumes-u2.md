---
title: StorSimple-kötetek kezelése (3. frissítés)
description: Ismerteti, hogyan lehet StorSimple-kötetek hozzáadását, módosítását, figyelését és törlését, valamint szükség esetén offline állapotba helyezni őket.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 12/09/2017
ms.author: alkohli
ms.openlocfilehash: 3d649b54b544c120198963f8094764e9590e20b2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100547542"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-volumes-update-3-or-later"></a>A kötetek kezelése a StorSimple Eszközkezelő szolgáltatásával (3. frissítés vagy újabb)

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag azt ismerteti, hogy a StorSimple Eszközkezelő szolgáltatás használatával hogyan hozhat létre és kezelhet köteteket a 3. frissítést és újabb verziót futtató StorSimple 8000 sorozatú eszközökön.

A StorSimple Eszközkezelő szolgáltatás a Azure Portal egyik bővítménye, amely lehetővé teszi a StorSimple-megoldás egyetlen webes felületen való kezelését. A Azure Portal használatával kezelheti a köteteket az összes eszközön. Emellett StorSimple-szolgáltatásokat is létrehozhat és kezelhet, kezelheti az eszközöket, a biztonsági mentési házirendeket és a biztonságimásolat-katalógust, és megtekintheti a riasztásokat.

## <a name="volume-types"></a>Kötetek típusai

A StorSimple kötetek a következőket tehetik:

* **Helyileg rögzített kötetek**: ezekben a kötetekben lévő adatforgalom mindig a helyi StorSimple-eszközön marad.
* **Lépcsőzetes kötetek**: ezekben a kötetekben lévő adatforgalom a felhőbe kerül.

Az archiválási kötetek a rétegekből álló kötetek típusai. Az archiválási kötetek nagyobb mennyiségű deduplikált mérete lehetővé teszi, hogy az eszköz nagyobb adatszegmenseket továbbítson a felhőbe.

Ha szükséges, módosíthatja a kötet típusát a Helyiről a lépcsőzetesre vagy a rétegekből a helyire. További információért lépjen [a kötet típusának módosítása](#change-the-volume-type)elemre.

### <a name="locally-pinned-volumes"></a>Helyileg rögzített kötetek

A helyileg rögzített kötetek olyan teljes körűen kiosztott kötetek, amelyek nem képesek a felhőbe irányuló adatforgalomra, így biztosítva az elsődleges és a felhőalapú kapcsolatoktól független helyi garanciákat. A helyileg rögzített köteteken lévő adatmennyiség nem deduplikált és tömörítve van; a helyileg rögzített kötetek pillanatképei azonban deduplikálva vannak. 

A helyileg rögzített kötetek teljesen kiépítve; Ezért a létrehozásakor elegendő lemezterülettel kell rendelkeznie az eszközön. A StorSimple 8100-eszközön legfeljebb 8 TB méretű, helyileg rögzített köteteket hozhat létre, a 8600-es eszközön pedig 20 TB-ot. A StorSimple a pillanatképek, a metaadatok és az adatfeldolgozás érdekében fenntartja a fennmaradó helyi területet az eszközön. Megnövelheti egy helyileg rögzített kötet méretét a rendelkezésre álló maximális szabad területre, de a létrehozott kötetek mérete nem csökkenthető.

Ha helyileg rögzített kötetet hoz létre, a rendszer lerövidíti a rétegekből álló kötetek létrehozásához szükséges helyet. A fordított érték szintén igaz: Ha a már meglévő rétegű kötetekkel rendelkezik, a helyileg rögzített kötetek létrehozásához rendelkezésre álló terület kisebb lesz, mint a fent említett határértékek. A helyi kötetekkel kapcsolatos további információkért tekintse [meg a helyileg rögzített köteteken feltett gyakori kérdéseket](storsimple-8000-local-volume-faq.md).

### <a name="tiered-volumes"></a>Rétegzett kötetek

A többplatformos kötetek olyan dinamikusan kiosztott kötetek, amelyekben a gyakran használt adatforgalom helyi szinten marad az eszközön, és a ritkábban felhasznált adatmennyiségeket a rendszer automatikusan a felhőbe tömöríti. A dinamikus kiépítés olyan virtualizációs technológia, amelyben a rendelkezésre álló tár úgy tűnik, hogy meghaladja a fizikai erőforrásokat. Ahelyett, hogy a megfelelő tárterületet nem kívánja megtartani, a StorSimple dinamikus kiosztást használ, hogy elegendő helyet foglaljon le az aktuális követelmények kielégítése érdekében. A felhőalapú tárolás rugalmas jellege megkönnyíti ezt a megközelítést, mivel a StorSimple növelheti vagy csökkentheti a Felhőbeli tárolást a változó igények kielégítése érdekében.

Ha archivált adatmennyiséget használ a rétegekből, jelölje be a **kötet használata ritkábban használt archiválási** adatforgalomhoz jelölőnégyzetet, ha a kötet deduplikálása méretének méretét 512 kb-ra szeretné módosítani. Ha nem jelöli be ezt a beállítást, a megfelelő rétegű kötet 64 KB-os méretet fog használni. Egy nagyobb deduplikációs adattömbméret lehetővé teszi az eszköz számára a nagy mennyiségű archiválási adatok továbbítását a felhőbe.


### <a name="provisioned-capacity"></a>Kiosztott kapacitás

A következő táblázat tartalmazza az egyes eszközök és kötetek típusának maximális kiosztott kapacitását. (Vegye figyelembe, hogy a helyileg rögzített kötetek nem érhetők el egy virtuális eszközön.)

| Típus | Maximális rétegű kötet mérete | Maximális helyileg rögzített kötet mérete |
| --- | --- | --- |
| **Fizikai eszközök** | | |
| 8100 |64 TB |8 TB |
| 8600 |64 TB |20 TB |
| **Virtuális eszközök** | | |
| 8010 |30 TB |N/A |
| 8020 |64 TB |N/A |

## <a name="the-volumes-blade"></a>A kötetek panel

A **kötetek** panel lehetővé teszi a Microsoft Azure StorSimple eszközön kiépített tárolási kötetek kezelését a kezdeményezők (kiszolgálók) számára. Megjeleníti a szolgáltatáshoz csatlakoztatott StorSimple-eszközökön található kötetek listáját.

 ![Kötetek lap](./media/storsimple-8000-manage-volumes-u2/volumes-list.png)

A kötetek számos attribútumból állnak:

* **Kötet neve** – egy leíró név, amelynek egyedinek kell lennie, és segít azonosítani a kötetet. Ez a név a figyelési jelentésekben is használatos, amikor egy adott kötetre szűr. A kötet létrehozása után nem nevezhető át.
* Status – online vagy offline **állapotú** lehet. Ha egy kötet offline állapotban van, nem látható azoknak a kezdeményezőknek (kiszolgálóknak), amelyek számára engedélyezett a kötet használata.
* **Kapacitás** – a kezdeményező (kiszolgáló) által tárolható összes adatmennyiséget határozza meg. A helyileg rögzített kötetek teljes mértékben kiépítve és a StorSimple-eszközön helyezkednek el. A lépcsőzetes kötetek kiosztása dinamikusan történik, és az adatmennyiség deduplikált. A dinamikusan kiosztott kötetek esetében az eszköz nem rendeli hozzá a fizikai tárolókapacitást belsőleg vagy a felhőben a konfigurált kötet kapacitása alapján. A kötet kapacitása igény szerint van lefoglalva és felhasználva.
* **Type (típus** ) – azt jelzi, hogy a kötet **Lépcsőzetes** (alapértelmezett) vagy **helyileg rögzített**-e.

Az oktatóanyag utasításait az alábbi feladatok végrehajtásához használhatja:

* Kötet hozzáadása 
* Kötet módosítása 
* A kötet típusának módosítása
* Kötet törlése 
* Kötet offline állapotba helyezése 
* Kötet figyelése 

## <a name="add-a-volume"></a>Kötet hozzáadása

A StorSimple 8000 Series-eszköz üzembe helyezése során [létrehozott egy kötetet](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) . A kötetek hozzáadása hasonló eljárás.

#### <a name="to-add-a-volume"></a>Kötet hozzáadása

1. Az **Eszközök** panelen szereplő eszközök táblázatos listájából válassza ki az eszközt. Kattintson a **+ Kötet hozzáadása** gombra.

    ![Új kötet hozzáadása](./media/storsimple-8000-manage-volumes-u2/add-volume-01.png)

2. A **Kötet hozzáadása** panelen:
   
    1. Az **Eszköz kiválasztása** mezőt a rendszer automatikusan kitölti a jelenlegi eszközzel.

    2. A legördülő listából válassza ki a kötettárolót, amelyhez kötetet szeretne adni.

    3.  Adja meg a kötet **Nevét**. A kötet létrehozása után a kötet nem nevezhető át.

    4. A legördülő listából adja meg a **Típus** beállítást a kötethez. Helyi garanciákat, kis késleltetést és magasabb teljesítményt igénylő munkaterheléseknél válasszon **Helyileg rögzített** kötetet. Minden más adathoz válasszon **Rétegzett** kötetet. Ha archív adatokhoz használja ezt a kötetet, jelölje be a **Kötet használata ritkábban használt archív adatokhoz** beállítást.
      
       A rétegzett kötetek kiosztása dinamikus, ezért gyorsan létrehozhatók. Archív adatokhoz használt rétegzett kötet esetében a **Kötet használata ritkábban használt archív adatokhoz** beállítás bejelölésével 512 KB-ra módosíthatja a kötet deduplikációs adattömbméretét. Ha ez a mező nincs bejelölve, az érintett rétegzett kötet 64 KB-os adattömbméretet használ. Egy nagyobb deduplikációs adattömbméret lehetővé teszi az eszköz számára a nagy mennyiségű archiválási adatok továbbítását a felhőbe.
       
       Egy helyileg rögzített kötet kiosztása állandó, és biztosítja, hogy a kötet elsődleges adatai helyben tárolódnak az eszközön, és nem kerülnek a felhőbe.  Ha gyors helyi kötetet hoz létre, az eszköz ellenőrzi a helyi rétegeken elérhető tárhelyet a kívánt méretű kötet kiosztásához. A helyileg rögzített kötet létrehozásához hozzátartozhat az eszközön levő adatok felhőbe küldése is, így a kötet létrehozása hosszú időt vehet igénybe. A teljes időtartam a kiosztott kötet méretétől, az elérhető hálózati sávszélességtől és az eszközön lévő adatoktól függ.

    5. Adja meg a kötethez tartozó **Kiosztott kapacitást**. Jegyezze fel a kiválasztott kötettípus alapján elérhető kapacitást. A megadott kötetméret nem haladhatja meg a rendelkezésre álló területet.
      
       A 8100-as eszközön gyors helyi kötetekhez legfeljebb 8,5 TB, rétegzett kötetekhez legfeljebb 200 TB területet oszthat ki. A nagyobb 8600-as eszközön gyors helyi kötetekhez legfeljebb 22,5 TB, rétegzett kötetekhez legfeljebb 500 TB területet oszthat ki. A rétegzett kötetek munkakészletének üzemeltetéséhez helyi terület szükséges az eszközön, ezért a gyors helyi kötetek létrehozása hatással van a rétegzett kötetek kiosztásához rendelkezésre álló területre. Ha tehát helyileg rögzített kötetet hoz létre, a rétegzett kötetek létrehozásához rendelkezésre álló terület lecsökken. Ha rétegzett kötetet hoz létre, akkor ehhez hasonlóan a gyors helyi kötetek létrehozásához rendelkezésre álló terület csökken.
      
       Ha a 8100-as eszközön kioszt egy 8,5 TB-os (vagyis a legnagyobb megengedett méretű) gyors helyi kötetet, azzal elfoglalja az összes rendelkezésre álló helyi területet az eszközön. Innentől kezdve nem hozhat létre további rétegzett köteteket, mivel nincs helyi terület az eszközön, amely üzemeltethetné a rétegzett kötet munkakészletét. A meglévő rétegzett kötetek is hatással vannak a rendelkezésre álló területre. Ha például egy 8100-as eszközhöz már tartozik körülbelül 106 TB rétegzett kötet, akkor csak 4 TB érhető el a gyors helyi kötetekhez.

    6. A **csatlakoztatott gazdagépek** mezőben kattintson a nyílra, majd válassza ki a csatlakoztatni kívánt ACR-ket. A **csatlakoztatott gazdagépek** panelen válasszon egy meglévő ACR-t, vagy adjon hozzá egy új ACR-t. Ha új ACR-t választ, akkor adjon meg egy **nevet** az ACR számára, adja meg a Windows-gazdagép **iSCSI minősített nevét** (IQN). Ha nem tudja az IQN-t, lépjen a Windows Server-gazdagép IQN nevének lekérése szakaszhoz.<!--Please verify: Is new ACR capability still available? Are the labels and controls the same?--> 

        ![Csatlakoztatott gazdagép hozzáadása](./media/storsimple-8000-manage-volumes-u2/add-volume-02.png)<!--New graphic. Source: add-volume-connected host-->

   7. A beállítások befejezését követően kattintson a **Létrehozás** gombra. 

      Létrejön egy kötet a megadott beállításokkal. Az új kötet készen áll a használatra.

> [!NOTE]
> Ha egy helyileg rögzített kötetet hoz létre, majd közvetlenül utána létrehoz egy másik helyileg rögzített kötetet, a kötet-létrehozási feladatok egymás után futnak. Az első kötet-létrehozási feladatoknak befejezniük kell a következő kötet-létrehozási feladatok elkezdése előtt.

## <a name="modify-a-volume"></a>Kötet módosítása

Módosítsa a kötetet, ha ki kell bontania, vagy módosítania kell a kötetet elérő gazdagépeket.

> [!IMPORTANT]
> * Ha módosítja az eszközön a kötet méretét, a kötet méretét is módosítani kell a gazdagépen.
> * Az itt leírt gazdagép-oldali lépések a Windows Server 2012 (2012R2) rendszerre vonatkoznak. A Linux vagy más gazdagép operációs rendszerek eljárásai eltérőek lesznek. Ha egy másik operációs rendszert futtató gazdagépen módosítja a kötetet, tekintse meg a gazdagép operációs rendszerének utasításait.

#### <a name="to-modify-a-volume"></a>Kötet módosítása

1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az eszközök táblázatos listájából válassza ki azt az eszközt, amelynek a kötetét módosítani kívánja. Kattintson a **beállítások > kötetek** elemre.

    ![Ugrás a kötetek panelre](./media/storsimple-8000-manage-volumes-u2/modify-volume-02.png)

2. A kötetek táblázatos listájából válassza ki a kötetet, és kattintson a jobb gombbal a helyi menü meghívásához. Válassza az **Offline** állapotba állítás lehetőséget, ha szeretné, hogy a kötet offline állapotba kerüljön.

    ![Válassza ki a kötetet, és állítsa offline állapotba](./media/storsimple-8000-manage-volumes-u2/modify-volume-04.png)

3. Tekintse át a **kötet offline** állapotba helyezésének következményeit, és jelölje be a megfelelő jelölőnégyzetet. Győződjön meg arról, hogy a megfelelő kötet a gazdagépen offline állapotban van. Arról, hogy miként lehet a köteteket offline állapotba helyezni a StorSimple csatlakozó gazdagép-kiszolgálón, tekintse meg az operációs rendszerre vonatkozó utasításokat. Kattintson az **offline állapotba** állítás elemre.

    ![A kötet offline állapotba helyezésének következményeinek áttekintése](./media/storsimple-8000-manage-volumes-u2/modify-volume-05.png)

4. Ha a kötet offline állapotban van (ahogy azt a kötet állapota is mutatja), válassza ki a kötetet, és kattintson a jobb gombbal a helyi menü meghívásához. Válassza a **Kötet módosítása** lehetőséget.

    ![Válassza a kötet módosítása lehetőséget.](./media/storsimple-8000-manage-volumes-u2/modify-volume-09.png)


5. A **Kötet módosítása** panelen a következő módosításokat végezheti el:
   
   1. A kötet **neve** nem szerkeszthető.
   2. Alakítsa át a **típust** a helyileg rögzítettről lépcsőzetesre, vagy a rétegektől a helyileg rögzített értékre (lásd: [a kötet típusának módosítása](#change-the-volume-type) további információkért).
   3. Növelje a **kiosztott kapacitást**. A **kiosztott kapacitást** csak növelni lehet. A kötetek létrehozása után nem lehet zsugorodni.
   4. A **csatlakoztatott gazdagépek** területen módosíthatja az ACR-t. Az ACR módosításához a kötetnek offline állapotban kell lennie.

       ![Tekintse át a kötet offline állapotba helyezésének következményeit 2](./media/storsimple-8000-manage-volumes-u2/modify-volume-11.png)<!--Legacy screen doesn't match step. New graphic needed?-->

6. Kattintson a **Mentés** gombra a módosítások mentéséhez. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra. A Azure Portal megjeleníti a kötet frissítési üzenetét. Ekkor megjelenik egy sikeres üzenet, ha a kötet sikeresen frissítve lett.

    ![Tekintse át a kötet offline állapotba helyezésének következményeit 3](./media/storsimple-8000-manage-volumes-u2/modify-volume-05.png)<!--Updated graphic. Source: modify-volume-save-->

7. Ha bővít egy kötetet, hajtsa végre a következő lépéseket a Windows-gazdagépen:
   
   1. Nyissa meg a **Számítógép-kezelés**  -> **Lemezkezelés webhelyét**.
   2. Kattintson a jobb gombbal a **Lemezkezelés** elemre, majd válassza a **Lemezek újraellenőrzése** elemet.
   3. A lemezek listájában válassza ki a frissített kötetet, kattintson a jobb gombbal, majd válassza a **Kötet kiterjesztése** lehetőséget. Elindul a Kötet kiterjesztése varázsló. Kattintson a **Tovább** gombra.
   4. Fejezze be a varázslót, és fogadja el az alapértelmezett értékeket. A varázsló befejezése után a kötetnek meg kell jelennie a megnövelt méretnek.
      
      > [!NOTE]
      > Ha később kibont egy helyileg rögzített kötetet, majd azonnal kibont egy másik helyileg rögzített kötetet, a kötet bővítési feladatai egymás után futnak. Az első kötet-bővítési feladatoknak befejezniük kell a következő kötet bővítési feladatainak megkezdése előtt.
      

## <a name="change-the-volume-type"></a>A kötet típusának módosítása

A kötet típusát megváltoztathatja a rétegek közötti, illetve a helyileg rögzített vagy a lépcsőzetesen rögzített értékről. Ez a konverzió azonban nem lehet gyakori előfordulás.

### <a name="tiered-to-local-volume-conversion-considerations"></a>A helyi kötetek átalakításának szempontjai

A kötetek rétegekből helyileg rögzítettre történő átalakításának néhány oka a következő:

* Az adatelérhetőséggel és a teljesítménnyel kapcsolatos helyi garanciák
* A Felhőbeli késések és a felhőalapú kapcsolódási problémák kiküszöbölése.

Ezek általában olyan kis méretű meglévő kötetek, amelyeket gyakran szeretne elérni. A helyileg rögzített kötetek létrehozásakor teljes mértékben kiépítve. 

Ha egy rétegű kötetet helyileg rögzített kötetre alakít át, a StorSimple ellenőrzi, hogy van-e elegendő hely az eszközön az átalakítás elindítása előtt. Ha nincs elég hely, hibaüzenetet kap, és a rendszer megszakítja a műveletet. 

> [!NOTE]
> Mielőtt elkezdi a lépcsőzetesről a helyileg rögzítettre való átalakítást, ügyeljen rá, hogy figyelembe vegye a többi számítási feladat lemezterület-követelményeit. 

A rétegekből egy helyileg rögzített kötetre történő átalakítás hátrányosan befolyásolhatja az eszköz teljesítményét. Emellett az alábbi tényezők növelhetik az átalakítás befejezéséhez szükséges időt:

* Nincs elegendő sávszélesség.
* Nincs aktuális biztonsági másolat.

A tényezők lehetséges hatásainak csökkentése:

* Tekintse át a sávszélesség-szabályozási szabályzatokat, és győződjön meg arról, hogy a dedikált 40 Mbps sávszélesség elérhető.
* Ütemezze az átalakítást a kikapcsolatlan órákra.
* Az átalakítás megkezdése előtt készítsen egy Felhőbeli pillanatképet.

Ha több kötet átalakítását (a különböző munkaterheléseket támogatja) használja, rangsorolja a kötet-átalakítást, hogy a magasabb prioritású kötetek legyenek konvertálva. A kötetek fájlmegosztás-munkaterhelésekkel való konvertálása előtt például olyan köteteket kell konvertálnia, amelyek a virtuális gépeket (VM) vagy a köteteket SQL-munkaterhelésekkel futtatják.

### <a name="local-to-tiered-volume-conversion-considerations"></a>Helyi – többtényezős kötetek konverziójának szempontjai

Előfordulhat, hogy egy helyileg rögzített kötetet egy lépcsőzetes kötetre kíván módosítani, ha további lemezterületre van szüksége a többi kötet kiépítéséhez. Amikor a helyileg rögzített kötetet lépcsőzetesre konvertálja, az eszközön rendelkezésre álló kapacitás a felszabadított kapacitás méretével nő. Ha a kapcsolati problémák akadályozzák meg a kötet átalakítását a helyi típusról a rétegű típusra, a helyi kötet a konvertálás befejezéséig a rétegekből álló kötetek tulajdonságait fogja mutatni. Ennek az az oka, hogy egyes adatsorok kiömlött a felhőbe. A kiömlött adatmennyiség továbbra is helyi helyet foglal az eszközön, amely a művelet újraindításáig és befejezésekor nem szabadítható fel.

> [!NOTE]
> A kötetek konvertálása hosszabb időt is igénybe vehet, és a kezdést követően nem lehet megszakítani az átalakítást. A kötet online állapotban marad az átalakítás során, és biztonsági másolatokat készíthet, de nem tudja kibontani vagy visszaállítani a kötetet, amíg az átalakítás zajlik.


#### <a name="to-change-the-volume-type"></a>A kötet típusának módosítása

1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az eszközök táblázatos listájából válassza ki azt az eszközt, amelynek a kötetét módosítani kívánja. Kattintson a **beállítások > kötetek** elemre.

    ![Ugrás a kötetek panel 2](./media/storsimple-8000-manage-volumes-u2/modify-volume-02.png)

3. A kötetek táblázatos listájából válassza ki a kötetet, és kattintson a jobb gombbal a helyi menü meghívásához. Válassza a **módosítás** lehetőséget.

    ![Válassza a módosítás a helyi menüből lehetőséget](./media/storsimple-8000-manage-volumes-u2/change-volume-type-02.png)

4. A **Kötet módosítása** panelen módosítsa a kötet típusát úgy, hogy kiválasztja az új típust a **típus** legördülő listából.
   
   * Ha a típust **helyileg rögzítettre** módosítja, a StorSimple azt vizsgálja, hogy van-e elegendő kapacitás.
   * Ha a típust **többrétegű** módosítja, és ezt a kötetet archivált adatként kívánja használni, jelölje be a **kötet használata a ritkábban használt archiválási** mezőkhöz jelölőnégyzetet.
   * Ha egy helyileg rögzített kötetet lépcsőzetes vagy _fordítva_ konfigurál, a következő üzenet jelenik meg.
   
     ![Kötet típusú üzenet módosítása](./media/storsimple-8000-manage-volumes-u2/change-volume-type-03.png)

7. Kattintson a **Mentés** gombra a módosítások mentéséhez. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra az átalakítási folyamat elindításához. 

    ![Mentés és megerősítés](./media/storsimple-8000-manage-volumes-u2/modify-volume-11.png)

8. A Azure Portal megjelenít egy értesítést a feladatokhoz, amely frissíti a kötetet. A kötet-átalakítási feladatok állapotának figyeléséhez kattintson az értesítésre.

    ![A kötet átalakításának feladata](./media/storsimple-8000-manage-volumes-u2/change-volume-type-05.png)

## <a name="take-a-volume-offline"></a>Kötet offline állapotba helyezése

Előfordulhat, hogy offline állapotba kell helyeznie egy kötetet, amikor módosítani vagy törölni szeretné a kötetet. Ha egy kötet offline állapotban van, nem érhető el írási és olvasási hozzáféréssel. A kötetet offline állapotba kell helyeznie a gazdagépen és az eszközön.

#### <a name="to-take-a-volume-offline"></a>Kötet offline állapotba helyezése

1. Az offline állapotba helyezés előtt győződjön meg arról, hogy a szóban forgó kötet nincs használatban.
2. Először állítsa offline állapotba a kötetet a gazdagépen. Ezzel kiküszöbölheti az adatsérülés lehetséges kockázatát a köteten. Adott lépésekért tekintse meg a gazdagép operációs rendszerének utasításait.
3. Miután a gazdagép offline állapotba került, az alábbi lépések végrehajtásával állítsa offline állapotba az eszközön a kötetet:
   
    1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az eszközök táblázatos listájából válassza ki azt az eszközt, amelynek a kötetét módosítani kívánja. Kattintson a **beállítások > kötetek** elemre.

        ![Ugrás a kötetek panel 3](./media/storsimple-8000-manage-volumes-u2/modify-volume-02.png)

    2. A kötetek táblázatos listájából válassza ki a kötetet, és kattintson a jobb gombbal a helyi menü meghívásához. Válassza az **Offline** állapotba állítás lehetőséget, ha szeretné, hogy a kötet offline állapotba kerüljön.

        ![Válassza ki a kötetet, és állítsa offline állapotba](./media/storsimple-8000-manage-volumes-u2/modify-volume-04.png)

3. Tekintse át a **kötet offline** állapotba helyezésének következményeit, és jelölje be a megfelelő jelölőnégyzetet. Kattintson az **offline állapotba** állítás elemre. 

    ![Tekintse át a kötet offline állapotba helyezésének következményeit 4](./media/storsimple-8000-manage-volumes-u2/modify-volume-05.png)
      
      Értesítést kap, ha a kötet offline állapotban van. A kötet állapota offline állapotra is frissül.
      
4. Ha a kötet offline állapotban van, és a jobb gombbal kattint, az **online** lehetőség elérhetővé válik a helyi menüben.

> [!NOTE]
> Az **Offline** állapotba állítás parancs egy kérést küld az eszköznek, hogy offline állapotba hozza a kötetet. Ha a gazdagépek továbbra is a kötetet használják, ez a művelet sérült kapcsolatokat eredményez, de a kötet offline állapotba helyezése nem fog sikerülni.

## <a name="delete-a-volume"></a>Kötet törlése

> [!IMPORTANT]
> Kötetet csak akkor törölhet, ha offline állapotban van.

A kötetek törléséhez hajtsa végre az alábbi lépéseket.

#### <a name="to-delete-a-volume"></a>Kötet törlése

1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az eszközök táblázatos listájából válassza ki azt az eszközt, amelynek a kötetét módosítani kívánja. Kattintson a **beállítások > kötetek** elemre.

    ![Ugrás a kötetek panel 4](./media/storsimple-8000-manage-volumes-u2/modify-volume-02.png)

3. Tekintse meg a törölni kívánt kötet állapotát. Ha a törölni kívánt kötet nem offline állapotban van, először állítsa offline állapotba. Kövesse a [kötet offline állapotba](#take-a-volume-offline)helyezése című témakör lépéseit.
4. Ha a kötet offline állapotban van, válassza ki a kötetet, kattintson a jobb gombbal a helyi menü meghívásához, majd válassza a **Törlés** lehetőséget.

    ![Válassza a törlés a helyi menüből lehetőséget](./media/storsimple-8000-manage-volumes-u2/delete-volume-01.png)

5. A **delete (Törlés** ) panelen tekintse át a kötetek törlésének következményeit, és jelölje be a jelölőnégyzetet. Ha töröl egy kötetet, a köteten lévő összes adat elvész. 

    ![Módosítások mentése és megerősítése](./media/storsimple-8000-manage-volumes-u2/delete-volume-02.png)

6. A kötet törlése után a kötetek táblázatos listája a törlés jelzésére szolgál.

    ![Frissített kötetek listája](./media/storsimple-8000-manage-volumes-u2/delete-volume-03.png)
   
   > [!NOTE]
   > Ha töröl egy helyileg rögzített kötetet, előfordulhat, hogy az új kötetek számára elérhető terület nem frissül azonnal. A StorSimple Eszközkezelő szolgáltatás rendszeresen frissíti a helyi helyet. Javasoljuk, hogy várjon néhány percet, mielőtt megpróbálja létrehozni az új kötetet.
   >
   > Emellett, ha töröl egy helyileg rögzített kötetet, majd azonnal töröl egy másik helyileg rögzített kötetet, a kötet-törlési feladatok egymás után futnak. Az első mennyiségi törlési feladatoknak befejezniük kell a következő kötet-törlési feladatok elkezdése előtt.

## <a name="monitor-a-volume"></a>Kötet figyelése

A mennyiségi figyelés lehetővé teszi a kötetek I/O-vel kapcsolatos statisztikáinak gyűjtését. A figyelés alapértelmezés szerint engedélyezve van a létrehozott első 32-kötetek esetében. A további kötetek figyelése alapértelmezés szerint le van tiltva. 

> [!NOTE]
> A klónozott kötetek figyelése alapértelmezés szerint le van tiltva.


A kötet figyelésének engedélyezéséhez vagy letiltásához hajtsa végre az alábbi lépéseket.

#### <a name="to-enable-or-disable-volume-monitoring"></a>A kötetek figyelésének engedélyezése vagy letiltása

1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az eszközök táblázatos listájából válassza ki azt az eszközt, amelynek a kötetét módosítani kívánja. Kattintson a **beállítások > kötetek** elemre.
2. A kötetek táblázatos listájából válassza ki a kötetet, és kattintson a jobb gombbal a helyi menü meghívásához. Válassza a **módosítás** lehetőséget.
3. A **Kötet módosítása** panelen a **figyeléshez** válassza az **Engedélyezés** vagy a **Letiltás** lehetőséget a figyelés engedélyezéséhez vagy letiltásához.

    ! [Figyelés letiltása] (./Media/storsimple-8000-Manage-Volumes-U2/monitor-Volume-0 1.png) 

4. Kattintson a **Mentés** gombra, és amikor a rendszer megerősítést kér, kattintson az **Igen** gombra. A Azure Portal megjelenít egy értesítést a kötet frissítéséről, majd egy sikeres üzenetről, miután a kötet frissítése sikeresen megtörtént.

## <a name="next-steps"></a>Következő lépések

* Tudnivalók a [StorSimple-kötetek klónozásáról](storsimple-8000-clone-volume-u2.md).
* Ismerje meg, hogyan kezelheti a [StorSimple-eszközt a StorSimple Eszközkezelő szolgáltatással](storsimple-8000-manager-service-administration.md).

