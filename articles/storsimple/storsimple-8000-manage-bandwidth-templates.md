---
title: A StorSimple 8000 Series sávszélesség-sablonjainak kezelése | Microsoft Docs
description: Útmutatás a StorSimple sávszélesség-sablonjainak kezeléséhez, amely lehetővé teszi a sávszélesség-felhasználás szabályozását.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 56170ffbbfe14248bcfd1f94549a3565873f8646
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005894"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-bandwidth-templates"></a>A StorSimple Eszközkezelő szolgáltatás használata a StorSimple sávszélesség-sablonjainak kezelésére

## <a name="overview"></a>Áttekintés

A sávszélesség-sablonok lehetővé teszik, hogy a hálózati sávszélesség használatát több időpontra konfigurálja a StorSimple-eszközről a felhőbe.

A sávszélesség-szabályozási ütemtervekkel a következőket teheti:

* A számítási feladatok hálózati kihasználtsága függvényében a testreszabott sávszélesség-ütemtervek megadása.
* Egyszerűen és zökkenőmentesen kezelheti a felügyeletet, és újra felhasználhatja az ütemterveket több eszközön.

> [!NOTE]
> Ez a funkció csak StorSimple fizikai eszközökön érhető el (8100-es és 8600-es modellek), és nem StorSimple Cloud Appliances (modellek 8010 és 8020).


## <a name="the-bandwidth-templates-blade"></a>A sávszélesség-sablonok panel

A **sávszélesség-sablonok** panel a szolgáltatáshoz tartozó összes sávszélesség-sablont táblázatos formában tartalmazza, és a következő információkat tartalmazza:

* **Név** – a létrehozáskor a sávszélesség-sablonhoz rendelt egyedi név.
* **Schedule (ütemezett** ) – az adott sávszélesség-sablonban található ütemtervek száma.
* **Használja** – a kötetek száma a sávszélesség-sablonok használatával.

További információkat is talál a következő témakörben található sávszélesség-sablonok konfigurálásához:

* [A sávszélesség-sablonokkal kapcsolatos kérdések és válaszok](#questions-and-answers-about-bandwidth-templates)
* [Ajánlott eljárások a sávszélesség-sablonokhoz](#best-practices-for-bandwidth-templates)

## <a name="add-a-bandwidth-template"></a>Sávszélesség-sablon hozzáadása

Új sávszélesség-sablon létrehozásához hajtsa végre az alábbi lépéseket.

#### <a name="to-add-a-bandwidth-template"></a>Sávszélesség-sablon hozzáadása

1. Lépjen a StorSimple Eszközkezelő szolgáltatáshoz, kattintson a **sávszélesség-sablonok** , majd a **+ sávszélesség-sablon hozzáadása** lehetőségre.

    ![Kattintson a + sávszélesség hozzáadása sablon elemre.](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp1.png)

2. A **sávszélesség-sablon hozzáadása** panelen hajtsa végre a következő lépéseket:
   
    1. Adja meg a sávszélesség-sablon egyedi nevét.
    2. Adja meg a sávszélesség-ütemtervet. Ütemterv létrehozása:
   
        1. A legördülő listában válassza ki a hét azon **napjait** , amelyekhez az ütemterv konfigurálva van. Több napot is kiválaszthat.        
        
        2. Adja meg a **kezdési időpontot** _óó: PP_ formátumban. Ez az az időpont, amikor az ütemterv elindul.

        3. Adja meg a **befejezési időpontot** _óó: PP_ formátumban. Ez az az időpont, amikor az ütemterv leáll.
      
           > [!NOTE]
           > Az átfedésben lévő ütemtervek nem engedélyezettek. Ha a kezdési és befejezési idő átfedésben van, akkor a rendszer hibaüzenetet jelenít meg.

        4. A **sávszélesség sebességének** meghatározása. Ez a StorSimple-eszköz által a felhővel kapcsolatos műveletekben használt sávszélesség (MB/s) (a feltöltések és a letöltések is). Ebben a mezőben 1–1000 közötti értéket adjon meg.

            ![Sávszélesség-ütemterv meghatározása](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp2.png)
         
            A fenti lépések megismétlésével több ütemezést is meghatározhat a sablonhoz, amíg el nem végzi a műveletet.

        5. A sávszélesség-sablon létrehozásához kattintson a **Hozzáadás** gombra. A létrehozott sablon hozzá lesz adva a sávszélesség-sablonok listájához.
      

## <a name="edit-a-bandwidth-template"></a>Sávszélesség-sablon szerkesztése

A sávszélesség-sablon szerkesztéséhez hajtsa végre az alábbi lépéseket.

### <a name="to-edit-a-bandwidth-template"></a>Sávszélesség-sablon szerkesztése

1. Lépjen a StorSimple Eszközkezelő szolgáltatásra, és kattintson a **sávszélesség-sablonok** elemre.
2. A sávszélesség-sablonok listájában válassza ki a törölni kívánt sablont. Kattintson a jobb gombbal, és a helyi menüben válassza a **Törlés** lehetőséget.
3. Ha a rendszer megerősítést kér, kattintson **az OK** gombra. Ennek a sávszélesség-sablont törölnie kell. 
4. A sávszélesség-sablonok frissítéseinek listája, amelyek a törlést tükrözik.

> [!NOTE]
> A módosítások nem menthetők, ha a szerkesztett ütemterv átfedésben van a módosított sávszélesség-sablonban meglévő ütemtervtel.

## <a name="delete-a-bandwidth-template"></a>Sávszélesség-sablon törlése

A sávszélesség-sablon törléséhez hajtsa végre az alábbi lépéseket.

#### <a name="to-delete-a-bandwidth-template"></a>Sávszélesség-sablon törlése

1. Lépjen a StorSimple Eszközkezelő szolgáltatásra, és kattintson a **sávszélesség-sablonok** elemre.
2. A sávszélesség-sablonok listájában válassza ki a törölni kívánt sablont. Kattintson a jobb gombbal, és a helyi menüben válassza a Törlés lehetőséget.
3. Ha a rendszer megerősítést kér, kattintson **az OK** gombra. Ennek a sávszélesség-sablont törölnie kell.
4. A sávszélesség-sablonok frissítéseinek listája, amelyek a törlést tükrözik.

Ha a sablont bármely kötet (ek) használja, nem törölheti. Megjelenik egy hibaüzenet, amely azt jelzi, hogy a sablon használatban van. Megjelenik egy hibaüzenet párbeszédpanel, amely tájékoztatja, hogy a sablonra vonatkozó összes hivatkozást el kell távolítani.

A sablonra mutató összes hivatkozást törölheti úgy, hogy hozzáfér a **mennyiségi tárolók** lapjához, és módosítja a sablont használó kötet-tárolókat, hogy egy másik sablont használjanak, vagy egyéni vagy korlátlan sávszélesség-beállítást használjanak. Ha az összes hivatkozás el lett távolítva, törölheti a sablont.

## <a name="use-a-default-bandwidth-template"></a>Alapértelmezett sávszélesség-sablon használata

A rendszer alapértelmezett sávszélesség-sablont biztosít, és a mennyiségi tárolók alapértelmezés szerint a felhőhöz való hozzáféréskor kényszerítik a sávszélesség-vezérlést. Az alapértelmezett sablon a saját sablonokat létrehozó felhasználók számára is kész referenciául szolgál. Az alapértelmezett sablon részletei a következők:

* **Név** – korlátlan számú éjszaka és hétvége
* **Schedule (ütemezett** ) – egy hétfőtől péntekig elvégezhető, 1 MB/s sebességű, 8 és 5 óra közötti sávszélességet alkalmazó menetrend. A sávszélesség a hét hátralévő részében korlátlan értékűre van állítva.

Az alapértelmezett sablon szerkeszthető. A sablon használata (beleértve a szerkesztett verziókat is) nyomon követhető.

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>Egy teljes napi sávszélesség-sablon létrehozása, amely egy adott időpontban indul

Az alábbi eljárással hozhat létre egy olyan ütemtervet, amely egy adott időpontban indul, és az egész napot futtatja. A példában az ütemezés reggel 9 ÓRAKOR kezdődik, és a következő reggel 9 órakor fut. Fontos megjegyezni, hogy egy adott ütemterv kezdési és befejezési időpontjának egyszerre kell szerepelnie ugyanazon a 24 órás időkereten belül, és nem lehet több napra kiterjednie. Ha több napra kiterjedő sávszélesség-sablonokat kell beállítania, több ütemtervet kell használnia (a példában látható módon).

#### <a name="to-create-an-all-day-bandwidth-template"></a>Teljes napi sávszélesség-sablon létrehozása

1. Hozzon létre egy ütemezést, amely reggel 9 ÓRAKOR kezdődik, és éjfélig fut.
2. Adjon hozzá egy másik ütemtervet. Konfigurálja a második ütemezést úgy, hogy éjféltől reggel 9 ÓRAKOR fusson.
3. Mentse a sávszélesség-sablont.

Az összetett ütemterv ezután a választott időpontban indul el, és az egész napot futtatja.

## <a name="questions-and-answers-about-bandwidth-templates"></a>A sávszélesség-sablonokkal kapcsolatos kérdések és válaszok

**K**. Mi történik a sávszélesség-szabályozással, ha az ütemtervek között van? (Egy ütemterv véget ér, és még egy másik még nem indult el.)

**A**. Ilyen esetekben a rendszer nem alkalmazza a sávszélesség-vezérlést. Ez azt jelenti, hogy az eszköz korlátlan sávszélességet használhat a felhőbe irányuló adatsorokban.

**K**. Megváltoztathatja a sávszélesség-sablonokat egy offline eszközön?

**A**. A kötetek tárolóján nem lehet módosítani a sávszélesség-sablonokat, ha a megfelelő eszköz offline állapotban van.

**K**. Szerkesztheti a kötet-tárolóhoz társított sávszélesség-sablont, ha a társított kötetek offline állapotban vannak?

**A**. Módosíthat egy olyan kötet-tárolóhoz társított sávszélesség-sablont, amelynek kötetei offline állapotban vannak. Vegye figyelembe, hogy ha a kötetek offline állapotban vannak, az eszközről a felhőbe nem kerül be az adatok.

**K**. Törölhető egy alapértelmezett sablon?

**A**. Bár törölheti az alapértelmezett sablont, nem célszerű ezt megtennie. A rendszer nyomon követ egy alapértelmezett sablon használatát, beleértve a szerkesztett verziókat is. A nyomkövetési adat elemzése és az idő előrehaladtával az alapértelmezett sablon javítására szolgál.

**K**. Hogyan állapítható meg, hogy a sávszélesség-sablonokat módosítani kell?

**A**. Az egyik jele, hogy módosítania kell a sávszélesség-sablonokat, amikor elkezdi látni a hálózatot, vagy többször is megfojtani egy nap alatt. Ha ez történik, figyelje a tárolási és a használati hálózatot úgy, hogy megtekinti az I/O-teljesítményt és a hálózati átviteli sebesség diagramokat.

A hálózati átviteli sebességtől függően azonosítsa a napi időpontot és a hálózati torlódást okozó mennyiségi tárolókat. Ha ez akkor fordul elő, ha az adatok a felhőbe vannak bontva (az eszközről a felhőbe irányuló összes kötet-tároló esetében az I/O-teljesítményről származó adatokat), akkor módosítania kell a mennyiségi tárolóhoz társított sávszélesség-sablonokat is.

Miután a módosított sablonok használatban vannak, jelentős késések esetén újra kell figyelnie a hálózatot. Ha ezek még léteznek, akkor újra kell látogatnia a sávszélesség-sablonokat.

**K**. Mi történik, ha az eszközön több mennyiségi tároló van olyan ütemtervtel, amely átfedésben van, de különböző korlátok vonatkoznak rájuk?

**A**. Tegyük fel, hogy van egy 3 kötet tárolóval rendelkező eszköz. A tárolókkal társított ütemtervek teljes mértékben átfedésben vannak. Mindegyik tároló esetében az 5, 10 és 15 Mbps-os sávszélesség-korlátot használja. Ha az összes ilyen tárolón előforduló I/O-műveletek egyszerre jelennek meg, a 3 sávszélesség-korlát minimuma alkalmazható: ebben az esetben 5 Mbps, mivel ezek a kimenő I/O-kérelmek ugyanazt a várólistát használják.

## <a name="best-practices-for-bandwidth-templates"></a>Ajánlott eljárások a sávszélesség-sablonokhoz

Kövesse az alábbi ajánlott eljárásokat a StorSimple-eszközhöz:

* Konfigurálja a sávszélesség-sablonokat az eszközön, hogy az eszköz különböző időpontokban beállítsa a hálózati átviteli sebesség változó szabályozását. Ezek a sávszélesség-sablonok a biztonsági mentési ütemtervek használata esetén a Felhőbeli műveletekhez további hálózati sávszélességet használhatnak a munkaidőn kívüli időszakokban.
* Kiszámítja az adott telepítéshez szükséges tényleges sávszélességet a központi telepítés méretétől és a szükséges helyreállítási időre vonatkozó célkitűzéstől (RTO) függően.

## <a name="next-steps"></a>Következő lépések

További információ [a StorSimple Eszközkezelő szolgáltatás a StorSimple-eszköz felügyeletéhez való használatáról](storsimple-8000-manager-service-administration.md).

