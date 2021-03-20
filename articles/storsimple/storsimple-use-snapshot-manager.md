---
title: StorSimple Snapshot Manager felhasználói felület | Microsoft Docs
description: Ismerteti a StorSimple Snapshot Manager felhasználói felületét, és ismerteti, hogyan használható a biztonsági mentési feladatok és a biztonsági mentési katalógus kezelésére.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: c7d91892-2881-41a2-a7a2-908dc3646493
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: 35fa0fc134045585eebd21324352f9b4ff272e87
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94954239"
---
# <a name="use-storsimple-snapshot-manager-user-interface-to-manage-backup-jobs-and-backup-catalog"></a>A StorSimple Snapshot Manager felhasználói felület használata a biztonsági mentési feladatok és a biztonságimásolat-katalógus kezeléséhez

## <a name="overview"></a>Áttekintés
A StorSimple Snapshot Manager intuitív felhasználói felülettel rendelkezik, amellyel elvégezheti és kezelheti a biztonsági mentéseket. Ez az oktatóanyag a felhasználói felület bevezetését mutatja be, majd leírja, hogyan használhatók az egyes összetevők. A StorSimple Snapshot Manager részletes ismertetését lásd: [Mi az a StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="console-description"></a>Konzol leírása
A felhasználói felület megtekintéséhez kattintson a StorSimple Snapshot Manager ikonjára az asztalon. Megjelenik a konzol ablaka, az alábbi ábrán látható módon.

![StorSimple Snapshot Manager ablaktáblák](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

A konzol ablakának öt fő eleme van. Kattintson a megfelelő hivatkozásra az egyes elemek teljes leírásához.

* [Menüsáv](#menu-bar) 
* [Eszköztár](#tool-bar) 
* [Hatókör panel](#scope-pane) 
* [Eredmények ablaktábla](#results-pane) 
* [Műveletek ablaktábla](#actions-pane) 

Emellett a StorSimple Snapshot Manager támogatja a [billentyűzetes navigálást és számos billentyűparancsot](#keyboard-navigation-and-shortcuts).

### <a name="console-accessibility"></a>Konzol kisegítő lehetőségei
A StorSimple Snapshot Manager felhasználói felülete támogatja a Windows operációs rendszer és a Microsoft Management Console (MMC) által biztosított kisegítő lehetőségeket, valamint néhány StorSimple Snapshot Manager-specifikus billentyűparancsot. 

* A Windows kisegítő lehetőségeinek leírását a [Windows billentyűparancsai](https://support.microsoft.com/kb/126449)című rész tartalmazza. 
* Az MMC kisegítő lehetőségeinek leírását a [kisegítő lehetőségek az mmc 3,0](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc766075(v=ws.11)) -hez című rész tartalmazza.
* A StorSimple Snapshot Manager kisegítő lehetőségekkel kapcsolatos további információkért lépjen a [billentyűzet navigáció és parancsikonok](#keyboard-navigation-and-shortcuts)elemre.

## <a name="menu-bar"></a>Menüsáv
A konzol ablakának tetején található menüsor [fájl](#file-menu), [művelet](#action-menu), [nézet](#view-menu), [Kedvencek](#favorites-menu), [ablak](#window-menu)és [Súgó](#help-menu) menüt tartalmaz.

A menüsor bármelyik elemére kattintva megjelenítheti az elérhető parancsok listáját a menüben. A következő példában a menüsoron kiválasztott **nézet** menü látható.

![Nézet menü kijelölve](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

### <a name="file-menu"></a>Fájl menü
A **fájl** menü szabványos Microsoft Management Console (MMC) parancsokat tartalmaz.

#### <a name="menu-access"></a>Menü-hozzáférés
A **fájl** menü megjelenítéséhez kattintson a menüsorban található **fájl** elemre. A következő menü jelenik meg.

![StorSimple Snapshot Manager Fájl menü](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png) 

#### <a name="menu-description"></a>Menü leírása
A következő táblázat a **fájl** menüben megjelenő elemeket ismerteti.

| Menüelem | Description |
|:--- |:--- |
| Új |Az **új** gombra kattintva hozzon létre egy új konzolt a StorSimple Snapshot Manager alapján. |
| Megnyitás |Egy meglévő konzol megnyitásához kattintson a **Megnyitás** gombra. |
| Mentés |Az aktuális konzol mentéséhez kattintson a **Mentés** gombra. |
| Mentés másként |Kattintson a **Save as (Mentés másként** ) lehetőségre az aktuális konzol új, átnevezett példányának létrehozásához. A **Mentés másként** lehetőséggel testre szabhatja a nézetet, és mentheti későbbi lekérésre. Létrehozhat például olyan StorSimple Snapshot Manager beépülő modulokat, amelyek adott kiszolgálókra mutatnak. |
| Beépülő modul hozzáadása/eltávolítása |Kattintson a **beépülő modul hozzáadása/eltávolítása** elemre beépülő modulok hozzáadásához vagy eltávolításához, illetve a csomópontok rendszerezéséhez a **hatókör** ablaktáblán. További információkért ugorjon a [beépülő modulok és bővítmények hozzáadása, eltávolítása és rendszerezése az MMC 3,0-ben](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc722035(v=ws.11))című témakörben. |
| Beállítások |Kattintson a **Beállítások lehetőségre** a konzol ikonjának módosításához, a felhasználói hozzáférési módok és engedélyek megadásához, vagy a konzol fájljainak törléséhez, hogy növelje a rendelkezésre álló lemezterületet. |
| Fájlelérési utak listája |Kattintson egy elérési útra a számozott listában egy nemrég megnyitott fájl újbóli megnyitásához. |
| Kilépés |Kattintson a **Kilépés** gombra a **fájl** menü bezárásához. |

### <a name="action-menu"></a>Művelet menü
Az elérhető műveletek közül válassza a **művelet** menüt. A rendelkezésre álló elemek a **hatókör** ablaktáblán vagy az **eredmények** ablaktábláján megadott kijelöléstől függenek.

#### <a name="menu-access"></a>Menü-hozzáférés
A **művelet** menü megjelenítéséhez tegye a következők egyikét:

* Kattintson a jobb gombbal egy elemre a **hatókör** ablaktáblán vagy az **eredmények** ablaktáblán.
* Jelöljön ki egy elemet a **hatókör** ablaktáblán vagy az **eredmények** ablaktáblán, majd kattintson a menüsávon a **művelet** elemre. 

Ha például a **hatókör** ablaktáblán a legfelső csomópontot választja, majd a menüsávon a jobb gombbal kattint vagy rákattint a **művelet** elemre, a következő menü jelenik meg.

![StorSimple Snapshot Manager művelet menü](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

A **műveletek** ablaktábla (a konzol jobb oldalán) ugyanazokat a műveleteket tartalmazza, mint a **művelet** menü. Emellett a **műveletek** ablaktábla tartalmazza a **nézet** menü beállításait, amelyek lehetővé teszik az **eredmények** ablaktábla egyéni nézetének létrehozását.

![Műveletek ablaktábla Megnyitás nézet menüjével](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

#### <a name="menu-description"></a>Menü leírása
A következő táblázat a StorSimple Snapshot Manager műveletek betűrendes listáját tartalmazza. 

* A **művelet** oszlop felsorolja a csomópontokon és eredményeken végrehajtható műveleteket. 
* A **navigációs** oszlop ismerteti, Hogyan jeleníthető meg a megfelelő **művelet** menü, így kiválaszthatja a műveletet. Egyes műveletek több **művelet** menüiben jelennek meg. Ehhez a műveletekhez válasszon ki egy **navigációs** lehetőséget a listajeles listából. 
* A **description (Leírás** ) oszlop leírja, hogyan használhatók az egyes műveletek a **művelet** menüben vagy a Műveletek ablaktáblán, és elmagyarázza, mit csinál.

> [!NOTE]
> A **műveletek** ablaktábla és **a műveleti** menük további beállításokat tartalmaznak, például **a nézet**, **az új ablak** innen, a **frissítés**, az **exportálási lista** és a **Súgó** elemre. Ezek a lehetőségek az MMC részeként érhetők el, és nem kifejezetten a StorSimple Snapshot Manager. A táblázat ezen beállítások leírását tartalmazza.
> 
> 

| Művelet | Navigáció | Description |
|:--- |:--- |:--- |
| Hitelesítés |Kattintson az **eszközök** csomópontra, majd kattintson a jobb gombbal az eszközre az **eredmények** ablaktábláján. |Kattintson a **hitelesítés** gombra az eszközhöz konfigurált jelszó megadásához. |
| Klónozás |Bontsa ki a **biztonsági mentési katalógus**, majd a **Felhőbeli Pillanatképek** csomópontot, kattintson egy elavult biztonsági mentésre, majd válasszon ki egy kötetet az **eredmények** ablaktáblán |Kattintson a **klónozás** gombra a Felhőbeli pillanatkép másolatának létrehozásához, és tárolja azt egy Ön által kijelölt helyen. |
| Eszköz konfigurálása |Kattintson a jobb gombbal az **eszközök** csomópontra. |Kattintson az **eszköz konfigurálása** lehetőségre egyetlen eszköz vagy több eszköz konfigurálásához a Windows-gazdagéphez való kapcsolódáshoz. |
| Biztonsági mentési szabályzat létrehozása |Tegye a következők egyikét:<ul><li>Kattintson a jobb gombbal a **biztonsági mentési házirendek** elemre.</li><li>Kattintson vagy bontsa ki a **kötetek csoportjai** elemet, majd kattintson a jobb gombbal a kötetre.</li><li>Kattintson vagy bontsa ki a **Backup Catalog** elemet, majd kattintson a jobb gombbal a kötet csoportra.</li></ul> |Kattintson a **biztonsági mentési szabályzat létrehozása** lehetőségre egy mennyiségi csoport ütemezett biztonsági mentésének konfigurálásához. |
| Kötet csoport létrehozása |Tegye a következők egyikét:<ul><li>Kattintson a **kötetek** csomópontra, majd kattintson a jobb gombbal egy kötetre az **eredmények** ablaktábláján.</li><li>Kattintson a jobb gombbal a **kötetek csoportjai** csomópontra.</li></ul> |Kattintson a **kötet létrehozása** elemre a kötetek kötethez való hozzárendeléséhez. |
| Törlés |Kattintson egy csomópontra vagy eredményre (ez az elem számos **művelet** menün és  **művelet** ablaktáblán jelenik meg.) |A kijelölt csomópont vagy eredmény törléséhez kattintson a **Törlés** gombra. Amikor megjelenik a megerősítő párbeszédpanel, erősítse meg vagy szakítsa meg a törlést. |
| Részletek |Kattintson az **eszközök** csomópontra, majd kattintson a jobb gombbal az eszközre az **eredmények** ablaktábláján. |A **részletek** elemre kattintva megtekintheti az eszköz konfigurációs adatait. |
| Szerkesztés |Kattintson a **biztonsági mentési házirendek** elemre, majd kattintson a jobb gombbal egy házirendre az **eredmények** ablaktábláján. |A kötetek biztonsági mentési ütemtervének módosításához kattintson a **Szerkesztés** gombra. |
| Exportálási lista |Kattintson bármelyik csomópontra vagy eredményre (ez az elem megjelenik az összes **művelet** menük és **műveletek** ablaktáblán.) |Kattintson a **lista exportálása** lehetőségre egy lista vesszővel tagolt (CSV-) fájlban való mentéséhez. Ezt követően importálhatja ezt a fájlt egy táblázatkezelő alkalmazásba elemzés céljából. |
| Help |Kattintson bármelyik csomópontra vagy eredményre. (Ez az elem megjelenik az összes **művelet** menük és **műveletek** ablaktáblán.) |Kattintson a **Súgó** lehetőségre az online súgó megnyitásához egy külön böngészőablakban. |
| Új ablak innen |Kattintson bármelyik csomópontra vagy eredményre (ez az elem megjelenik az összes **művelet** menük és **műveletek** ablaktáblán.) |Új StorSimple Snapshot Manager ablak megnyitásához kattintson **az új ablak** elemre. |
| Frissítés |Kattintson bármelyik csomópontra vagy eredményre (ez az elem megjelenik az összes **művelet** menük és **műveletek** ablaktáblán.) |Kattintson a **frissítés** gombra az aktuálisan megjelenített StorSimple Snapshot Manager ablak frissítéséhez. |
| Eszköz frissítése |Kattintson az **eszközök** csomópontra, majd kattintson a jobb gombbal az eszközre az **eredmények** ablaktábláján. |Kattintson az **eszköz frissítése** gombra egy adott csatlakoztatott eszköz StorSimple Snapshot Manager való szinkronizálásához. |
| Eszközök frissítése |Kattintson a jobb gombbal az **eszközök** csomópontra. |Kattintson az **eszközök frissítése** lehetőségre a csatlakoztatott eszközök listájának a StorSimple Snapshot Manager való szinkronizálásához. |
| Kötetek újraellenőrzése |Kattintson a jobb gombbal a **kötetek** csomópontra. |Kattintson a **kötetek újraellenőrzése** elemre az **eredmények** ablaktábláján megjelenő kötetek listájának frissítéséhez. |
| Visszaállítás |Bontsa ki a **biztonsági mentési katalógus**, a kötet csoport, a **helyi Pillanatképek** vagy a **Felhőbeli Pillanatképek** csomópontot, majd kattintson a jobb gombbal a biztonsági mentésre. |Kattintson a **visszaállítás** elemre a kötetek aktuális adatainak a kijelölt biztonsági másolatból származó adatokkal való lecseréléséhez. |
| Biztonsági mentés készítése |Tegye a következők egyikét:<ul><li>Bontsa ki a **kötetek csoportot**, majd kattintson a jobb gombbal a kötetre.</li><li>Bontsa ki a **Backup Catalog** csomópontot, majd kattintson a jobb gombbal a kötetre.</li></ul> |Kattintson a **biztonsági mentés** gombra a biztonsági mentési feladatok azonnali elindításához. |
| Importálások megjelenítésének kikapcsolása |A **hatókör** ablaktáblán kattintson a jobb gombbal a legfelső csomópontra (a példákban a **StorSimple Snapshot Manager** csomópontra). |Az **importálások megjelenítése** elemre kattintva megjelenítheti vagy elrejtheti a StorSimple Eszközkezelő szolgáltatás irányítópultján importált kötet-és társított biztonsági másolatokat. |

### <a name="view-menu"></a>Nézet menü
A **nézet** menüben hozhatja létre az **eredmények** ablaktábla tartalmának egyéni nézetét. A **nézet** menüben az **Oszlopok hozzáadása/eltávolítása** és a **testreszabási** beállítások szerepelnek.

#### <a name="menu-access"></a>Menü-hozzáférés
A **nézet** menü a menüsávon vagy a **műveletek** ablaktáblán érhető el.

![StorSimple Snapshot Manager Nézet menü](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png) 

#### <a name="menu-description"></a>Menü leírása
A következő táblázat a **nézet** menüben megjelenő elemeket ismerteti.

| Menüelem | Description |
|:--- |:--- |
| Oszlopok hozzáadása/eltávolítása |Az oszlopok hozzáadásához vagy eltávolításához kattintson az oszlopok **hozzáadása/eltávolítása** elemre az **eredmények** ablaktábláján. |
| Testreszabás |Kattintson a **Testreszabás** elemre az elemek megjelenítéséhez vagy elrejtéséhez a StorSimple Snapshot Manager-konzol ablakban. |

### <a name="favorites-menu"></a>Kedvencek menü
A **Kedvencek** menü használatával hozzáadhat, eltávolíthat és rendszerezheti a gyakran használt lapok nézeteit és feladatait. 

#### <a name="menu-access"></a>Menü-hozzáférés
A **Kedvencek** menüt a menüsávon érheti el.

![StorSimple Snapshot Manager Kedvencek menü](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

#### <a name="menu-description"></a>Menü leírása
A következő táblázat a **Kedvencek** menüben megjelenő elemeket ismerteti.

| Menüelem | Description |
|:--- |:--- |
| Hozzáadás a kedvencekhez |Kattintson a **Hozzáadás a kedvencekhez** gombra, hogy hozzáadja az aktuális nézetet a kedvencek listájához. |
| Kedvencek rendszerezése |Kattintson a **Kedvencek rendszerezése** lehetőségre a Kedvencek mappa tartalmának rendszerezéséhez. |

### <a name="window-menu"></a>Ablak menü
A Windows Snapshot Manager StorSimple hozzáadásához és átrendezéséhez használja a **ablak** menüt.

#### <a name="menu-access"></a>Menü-hozzáférés
A menüsor **ablak** menüjében is elérhető.

![StorSimple Snapshot Manager ablak menü](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

A menü alján található számozott lista a jelenleg megnyitott Windows-t jeleníti meg. Kattintson a lista bármelyik ablakára az ablak előtérben való bekapcsolásához. 

#### <a name="menu-description"></a>Menü leírása
A következő táblázat a ablak menüjében megjelenő elemeket ismerteti.

| Menüelem | Description |
|:--- |:--- |
| Új ablak |Kattintson az **új ablak** lehetőségre egy új konzolablak megnyitásához (a meglévő ablakon kívül). |
| Cascade |Kattintson a **Cascade (lépcsőzetes** ) elemre a megnyitott konzol ablakának egy lépcsőzetes stílusban való megjelenítéséhez. |
| Vízszintes csempe |Kattintson a **csempe vízszintesen** lehetőségre, hogy a megnyitott konzol ablakait csempe (vagy rács) formátumban jelenítse meg. |
| Ikonok elrendezése |Ha több konzolablak van megnyitva és szétszórva az asztalon, csökkentse a fájlokat, majd kattintson az **ikonok elrendezése** gombra a képernyő alján található vízszintes sorba rendezéshez. |

### <a name="help-menu"></a>Súgó menü
A **Súgó** menü használatával megtekintheti a StorSimple Snapshot Manager és az MMC-hez elérhető online súgót. Megtekintheti a rendszeren jelenleg telepített MMC-és StorSimple Snapshot Manager szoftververzió adatait is. 

A menüsávon a **Súgó** menüt érheti el. A StorSimple Snapshot Manager a **műveletek** ablaktáblán is elérheti a súgótémaköröket.

![StorSimple Snapshot Manager Súgó menü](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

#### <a name="menu-description"></a>Menü leírása
A következő táblázat a Súgó menüben megjelenő elemeket ismerteti.

| Menüelem | Description |
|:--- |:--- |
| Súgó a StorSimple Snapshot Manager |Kattintson a **Súgó a StorSimple Snapshot Manager** lehetőségre a StorSimple Snapshot Manager súgójának egy külön ablakban való megnyitásához. |
| Súgótémakörök |Kattintson a **súgótémakörök** lehetőségre az MMC online súgójának különálló ablakban való megnyitásához. |
| TechCenter webhely |Kattintson a **TechCenter** webhely elemre a Microsoft TechNet Tech Center kezdőlapjának megnyitásához egy külön ablakban. |
| Tudnivalók a Microsoft Management Console-ról |A Microsoft Management **Console névjegye** elemre kattintva megtekintheti, hogy a Microsoft Management Console melyik verziója van telepítve a rendszeren. |
| Tudnivalók a StorSimple Snapshot Manager |Kattintson a **StorSimple Snapshot Manager** elemre, és ellenőrizze, hogy a beépülő modul melyik verziója van telepítve a rendszeren. |

## <a name="tool-bar"></a>Eszköztár
A menüsáv alatt lévő eszköztár navigációs és feladat ikonokat tartalmaz. Mindegyik ikon egy adott feladathoz tartozó parancsikon.

### <a name="icon-descriptions"></a>Ikon leírása
Az alábbi táblázat az eszköztáron megjelenő ikonokat ismerteti. 

| Ikon | Description |
|:--- |:--- |
| ![Balra nyíl](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) |Kattintson a balra mutató nyíl ikonra az előző lapra való visszatéréshez. |
| ![Jobb nyílbillentyű](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) |Kattintson a jobbra mutató nyílra a következő lapra való ugráshoz (ha a nyíl szürke, a művelet nem érhető el). |
| ![Felfelé ikon](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) |Kattintson a fel ikonra egy szint megugrásához a konzolfán (a **hatókör** ablaktáblán). |
| ![Konzolfa megjelenítése/elrejtése](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) |Kattintson a konzolfa megjelenítése/elrejtése ikonra a **hatókör** ablaktábla megjelenítéséhez vagy elrejtéséhez. |
| ![Exportálási lista](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) |A lista exportálása ikonra kattintva exportálhat egy listát a megadott CSV-fájlba. |
| ![Súgó ikon](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png) |Kattintson a Súgó ikonra egy online MMC-súgótémakör megnyitásához. |
| ![Műveletek ablaktábla megjelenítése/elrejtése](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) |A **műveletek** ablaktábla megjelenítéséhez vagy elrejtéséhez kattintson a **műveletek** megjelenítése/elrejtése ikonra. |

## <a name="scope-pane"></a>Hatókör panel
A **hatókör** ablaktábla a StorSimple Snapshot Manager felhasználói felületének bal szélső panelje. A konzol (vagy csomópont) fáját tartalmazza, és a StorSimple Snapshot Manager elsődleges navigációs mechanizmusa. 

### <a name="scope-pane-structure"></a>Hatókör ablaktábla szerkezete
A **hatókör** ablaktábla egy fastruktúrában rendezett, kattintható objektumok (csomópontok) sorozatát tartalmazza. 

![Hatókör panel](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png) 

* Csomópont kibontásához vagy összecsukásához kattintson a csomópont neve melletti nyíl ikonra.
* Egy csomópont állapotának vagy tartalmának megtekintéséhez kattintson a csomópont nevére. Az adatok az **eredmények** ablaktábláján jelennek meg. 

A **hatókör** ablaktábla a következő csomópontokat tartalmazza: 

* [Eszközök csomópont](#devices-node) 
* [Kötetek csomópont](#volumes-node) 
* [Kötetek csoportjai csomópont](#volume-groups-node) 
* [Biztonsági mentési szabályzatok csomópont](#backup-policies-node) 
* [Biztonságimásolat-katalógus csomópontja](#backup-catalog-node) 
* [Feladatok csomópont](#jobs-node) 

### <a name="scope-pane-tasks"></a>Hatókör panel feladatai
A **hatókör** ablaktáblán egy adott csomóponton végezheti el a műveletet. Egy feladat kiválasztásához tegye a következők egyikét:

* Kattintson a jobb gombbal a csomópontra, majd a megjelenő menüben válassza ki a feladatot.
* Kattintson a csomópontra, majd a menüsorban kattintson a **művelet** elemre. Válassza ki a feladatot a megjelenő menüben.
* Kattintson a csomópontra, majd válassza ki a műveletet a **műveletek** ablaktáblán.

Ha kijelöl egy csomópontot, és a fenti módszerek bármelyikével megjeleníti a feladatlistát, akkor csak az adott csomóponton végrehajtható műveletek jelennek meg.

### <a name="devices-node"></a>Eszközök csomópont
A **Devices (eszközök** ) csomópont a StorSimple Snapshot Managerhoz kapcsolódó StorSimple-eszközöket és StorSimple virtuális eszközöket jelöli. Válassza ezt a csomópontot egy eszköz csatlakoztatásához és konfigurálásához, valamint a hozzá tartozó kötetek, kötetek csoportjai és meglévő biztonsági másolatok importálásához. Több eszköz is csatlakoztatható egyetlen gazdagéphez.

* A csomópont kibontásához kattintson az **eszközök** elem melletti nyíl ikonra.
* Az elérhető műveletek menüjének megtekintéséhez kattintson a jobb gombbal az **eszközök** csomópontra, vagy kattintson a jobb gombbal a kibontott nézetben megjelenő csomópontokra.
* A konfigurált eszközök listájának megtekintéséhez kattintson az **eszközök** elemre a **hatókör** ablaktáblán. Az eszközök listája, valamint az egyes eszközökre vonatkozó információk az **eredmények** ablaktábláján jelennek meg.

### <a name="volumes-node"></a>Kötetek csomópont
A **kötetek** csomópont a gazdagép által csatlakoztatott köteteknek megfelelő meghajtókat jelöli, beleértve az iSCSI-n keresztül felderített és az eszközön keresztül felderített lemezeket. Ezzel a csomóponttal megtekintheti a rendelkezésre álló kötetek listáját, és egyedi köteteket rendelhet hozzá a kötetek csoportjához.

* A csomópont kibontásához kattintson a **kötetek** melletti nyíl ikonra.
* Az elérhető műveletek menüjének megtekintéséhez kattintson a jobb gombbal a **kötetek** csomópontra, vagy kattintson a jobb gombbal a kibontott nézetben megjelenő csomópontokra.
* A kötetek listájának megtekintéséhez kattintson a **kötetek** elemre a **hatókör** ablaktáblán. A kötetek listája, valamint az egyes kötetekkel kapcsolatos információk az **eredmények** ablaktábláján jelennek meg.

### <a name="volume-groups-node"></a>Kötetek csoportjai csomópont
A mennyiségi csoportok más néven konzisztencia-csoportok. Az egyes mennyiségi csoportok az alkalmazással kapcsolatos kötetek készletei, amelyek segítségével biztosítható az alkalmazások konzisztenciája a biztonsági mentési műveletek során. A **kötetek csoportok** csomópont használatával konfigurálhatja ezeket a csoportokat, és interaktív biztonsági mentést készíthet, vagy biztonsági mentési ütemezéseket hozhat létre. 

* A csomópont kibontásához kattintson a **kötetek csoportok** melletti nyíl ikonra.
* Az elérhető műveletek menüjének megtekintéséhez kattintson a jobb gombbal a **kötetek csoportjai** csomópontra, vagy kattintson a jobb gombbal a kibontott nézetben megjelenő csomópontokra.
* A mennyiségi csoportok listájának megtekintéséhez kattintson a **kötetek csoportok** elemre a **hatókör** ablaktáblán. A mennyiségi csoportok listája és az egyes kötetek adatai együtt az **eredmények** ablaktábláján jelennek meg.

### <a name="backup-policies-node"></a>Biztonsági mentési szabályzatok csomópont
A biztonsági mentési házirendek a helyi és a Felhőbeli pillanatképekhez tartozó feladatütemezés. A biztonsági mentési **házirendek** csomópont segítségével megadhatja, hogy a rendszer milyen gyakran készítsen biztonsági mentést, és mennyi ideig őrizze meg a biztonsági mentést. 

* A csomópont kibontásához kattintson a **biztonsági mentési szabályzatok** melletti nyíl ikonra.
* Az elérhető műveletek menüjének megtekintéséhez kattintson a jobb gombbal a **biztonsági mentési házirendek** csomópontra, vagy kattintson a jobb gombbal a kibontott nézetben megjelenő csomópontokra.
* A biztonsági mentési szabályzatok listájának megtekintéséhez kattintson a **hatókör** ablaktáblán található **biztonsági mentési házirendek** elemre. A biztonsági mentési házirendek listája, valamint az egyes szabályzatokkal kapcsolatos információk az **eredmények** ablaktábláján jelennek meg.

> [!NOTE]
> Maximálisan 64 biztonsági mentést is megtarthat.


### <a name="backup-catalog-node"></a>Biztonságimásolat-katalógus csomópontja
A **Backup Catalog** csomópont az Azure StorSimple-kötetek helyszíni és kívüli biztonsági másolatainak listáját tartalmazza. Ezt a csomópontot mennyiségi csoport szerint rendezi a rendszer, és minden mennyiségi csoport tároló különálló struktúrákat tartalmaz a helyi pillanatképekhez (a **helyi Pillanatképek** csomópontja) és a Felhőbeli pillanatképekhez (a **Felhőbeli Pillanatképek** csomóponthoz). Ha kibontja, minden egyes mennyiségi csoport tároló felsorolja az interaktív módon vagy egy konfigurált szabályzatban végrehajtott összes sikeres biztonsági mentést.

* A csomópont kibontásához kattintson a **biztonsági mentési katalógus** melletti nyíl ikonra.
* Az elérhető műveletek menüjének megjelenítéséhez kattintson a jobb gombbal a **biztonsági mentési katalógus** csomópontra, vagy kattintson a jobb gombbal a kibontott nézetben megjelenő csomópontokra.
* A biztonsági mentési Pillanatképek listájának megtekintéséhez kattintson a **biztonsági mentés katalógus** elemre a **hatókör** ablaktáblán. A pillanatképek listája, valamint az egyes pillanatképekkel kapcsolatos információk az **eredmények** ablaktábláján jelennek meg.

### <a name="local-snapshots-node"></a>Helyi Pillanatképek csomópont
A **helyi Pillanatképek** csomópont egy adott mennyiségi csoport helyi pillanatképeit listázza. A csomópont a **hatókör** ablaktábla **biztonsági mentési katalógus** csomópontja alatt található. A helyi Pillanatképek az Azure StorSimple-eszközön tárolt mennyiségi adatmennyiségek időponthoz kötött példányai. Az ilyen típusú biztonsági másolatok általában gyorsan létrehozhatók és visszaállíthatók. Helyi biztonsági másolat készítésekor helyi pillanatképet is használhat.

* A csomópont kibontásához kattintson a **helyi Pillanatképek** melletti nyíl ikonra.
* Az elérhető műveletek menüjének megtekintéséhez kattintson a jobb gombbal a **helyi Pillanatképek** csomópontra, vagy kattintson a jobb gombbal a kibontott nézetben megjelenő csomópontokra.
* A helyi Pillanatképek listájának megtekintéséhez kattintson a **helyi Pillanatképek** elemre a **hatókör** ablaktáblán. A pillanatképek listája, valamint az egyes pillanatképekkel kapcsolatos információk az **eredmények** ablaktábláján jelennek meg.

### <a name="cloud-snapshots-node"></a>Felhőbeli Pillanatképek csomópont
A **Felhőbeli Pillanatképek** csomópont egy adott kötethez tartozó Felhőbeli pillanatképeket sorolja fel. A csomópont a **hatókör** ablaktábla **biztonsági mentési katalógus** csomópontja alatt található. A Felhőbeli Pillanatképek a felhőben tárolt mennyiségi adatmennyiségek időponthoz kötődő példányai. A Felhőbeli Pillanatképek egy másik, nem helyszíni tárolási rendszeren replikált pillanatképnek felelnek meg. A Felhőbeli Pillanatképek különösen hasznosak a vész-helyreállítási helyzetekben.

* A csomópont kibontásához kattintson a **Felhőbeli Pillanatképek** melletti nyíl ikonra.
* Az elérhető műveletek menüjének megjelenítéséhez kattintson a jobb gombbal a **Felhőbeli Pillanatképek** csomópontra, vagy kattintson a jobb gombbal a kibontott nézetben megjelenő csomópontokra.
* A Felhőbeli Pillanatképek listájának megtekintéséhez kattintson a **Felhőbeli Pillanatképek** elemre a **hatókör** ablaktáblán. A pillanatképek listája, valamint az egyes pillanatképekkel kapcsolatos információk az **eredmények** ablaktábláján jelennek meg.

### <a name="jobs-node"></a>Feladatok csomópont
A **feladatok** csomópont az ütemezett, a futó és a legutóbb befejezett biztonsági mentési feladatokkal kapcsolatos információkat tartalmaz. 

* A csomópont kibontásához kattintson a **feladatok** elem melletti nyíl ikonra.
* Az elérhető műveletek menüjének megtekintéséhez kattintson a jobb gombbal a **feladatok** csomópontra, vagy kattintson a jobb gombbal a kibontott nézetben megjelenő csomópontokra.
* Az ütemezett feladatok listájának megtekintéséhez bontsa ki a **feladatok** csomópontot, majd kattintson az **ütemezett** gombra. Az egyes feladatokkal kapcsolatos korábban konfigurált feladatok és információk listája az **eredmények** ablaktábláján jelenik meg. 
* A legutóbb Befejezett feladatok listájának megtekintéséhez bontsa ki a **feladatok** csomópontot, majd kattintson az **elmúlt 24 óra** elemre. Az elmúlt 24 órában Befejezett feladatok listája az **eredmények** ablaktábláján jelenik meg. Az **eredmények** ablaktábla az egyes befejezett feladatokkal kapcsolatos információkat is tartalmaz.
* A jelenleg futó feladatok listájának megtekintéséhez bontsa ki a **feladatok** csomópontot, majd kattintson a **Futtatás** elemre. Az aktuálisan futó feladatok listája és az egyes feladatokkal kapcsolatos információk az **eredmények** ablaktábláján jelennek meg.

## <a name="results-pane"></a>Eredmények ablaktábla
Az **eredmények** ablaktábla a StorSimple középső ablaktáblája SNAPSHOT Manager felhasználói felület. Felsorolja és részletes állapotinformációkat tartalmaz a **hatókör** ablaktáblán kiválasztott csomópontról.

### <a name="example"></a>Példa
A következő példa megjelenítéséhez kattintson a **kötetek csoportok** csomópontra a **hatókör** ablaktáblán. Az **eredmények** ablaktábla megjeleníti az egyes csoportok adatait tartalmazó kötetek listáját.

![Eredmények ablaktábla](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png) 

Az **eredmények** ablaktáblán megjelenő részleteket konfigurálhatja: kattintson a jobb gombbal a csomópontra a **hatókör** ablaktáblán, kattintson a **nézet**, majd az **Oszlopok hozzáadása/eltávolítása** elemre.

## <a name="actions-pane"></a>Műveletek ablaktábla
A **műveletek** ablaktábla a StorSimple Snapshot Manager felhasználói felületének jobb oldali ablaktáblája. A művelet a **hatókör** ablaktáblán vagy az **eredmények** ablaktábláján kiválasztott csomóponton, nézetben vagy adatokon végrehajtható műveletek menüjét tartalmazza. A **műveletek** ablaktábla ugyanazokat a parancsokat tartalmazza, mint a **hatókör** ablaktáblán és az **eredmények** ablaktáblán található elemekhez elérhető **műveleti** menük. Az egyes műveletek leírását a **művelet** menü szakaszának táblázatában találja.

### <a name="examples"></a>Példák
A következő példa megtekintéséhez a **hatókör** ablaktáblán bontsa ki a **feladatok** csomópontot, majd kattintson az **ütemezett** elemre. A **műveletek** ablaktábla megjeleníti az **ütemezett** csomóponthoz elérhető műveleteket.

![Műveletek ablaktábla ütemezett feladatok – példa](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png) 

További beállítások megjelenítéséhez a **hatókör** ablaktáblán bontsa ki a **feladatok** csomópontot, kattintson az **ütemezett** elemre, majd az **eredmények** ablaktáblájában kattintson egy ütemezett feladatra. A **műveletek** ablaktábla a következő példában látható módon megjeleníti az ütemezett feladatokhoz elérhető műveleteket.

![Műveletek ablaktábla feladatok – példa](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

## <a name="keyboard-navigation-and-shortcuts"></a>Billentyűparancsok és billentyűparancsok
A StorSimple Snapshot Manager lehetővé teszi a Windows operációs rendszer és a Microsoft Management Console (MMC) kisegítő funkcióinak használatát. Emellett az alábbi szakaszokban leírtak szerint a StorSimple Snapshot Managerra jellemző billentyűparancsokat és parancsikonokat is tartalmaz.

* [Billentyűzet navigációs kulcsai](#keyboard-navigation-keys) 
* [Menüsor billentyűparancsai](#menu-bar-shortcut-keys) 
* [A hatókör ablaktáblájának gyorsbillentyűi](#scope-pane-shortcut-keys) 

### <a name="keyboard-navigation-keys"></a>Billentyűzet navigációs kulcsai
A következő táblázat azokat a kulcsokat ismerteti, amelyeket a StorSimple Snapshot Manager felhasználói felületén való böngészéshez használhat. 

| Navigációs kulcs | Művelet |
|:--- |:--- |
| Lefelé nyíl gomb |A lefelé mutató nyíl gomb használatával függőlegesen mozoghat a menü vagy ablaktábla következő eleméhez. |
| Enter |Nyomja le az ENTER billentyűt egy művelet végrehajtásához, majd folytassa a következő lépéssel. Az ENTER billentyű lenyomásával például kiválaszthatja a **tovább**, **az OK** vagy a **Létrehozás** lehetőséget, majd a varázsló következő lépésére léphet. |
| Esc |Nyomja le az ESC billentyűt egy menü bezárásához, illetve egy oldal bezárásához. |
| F1 |Nyomja meg az F1 billentyűt a jelenleg aktív ablakhoz tartozó súgótémakör megtekintéséhez. |
| F5 |A csomópont frissítéséhez nyomja le az F5 billentyűt. |
| F6 |Az F6 billentyű lenyomásával lépjen a **hatókör** ablaktábláról az **eredmények** ablaktáblára. |
| F10 |Az F10 billentyű lenyomásával lépjen a menüsorra. |
| Balra nyíl |A bal nyíl gomb használatával vízszintesen áthelyezheti a menüsorból az előzőre mutató beállítást. Amikor a menüsor előző elemére lép, megjelenik az előző elemhez tartozó művelet (vagy környezet) menü. |
| Jobbra nyíl |A jobbra mutató nyíl használatával vízszintesen helyezhető át egy menüsorból a következőre. Ha a menüsáv következő elemére lép, megjelenik az új elem művelete (vagy kontextus) menü. |
| Tab billentyű |A TAB billentyűvel lépjen a konzol következő ablaktáblájára, vagy az oldal következő kijelölésére vagy szövegdobozára. |
| Felfelé nyíl gomb |A felfelé mutató nyílra kattintva függőlegesen áthelyezheti a menü vagy ablaktábla előző elemét. |

### <a name="menu-bar-shortcut-keys"></a>Menüsor billentyűparancsai
Az alábbi táblázat a menüsáv billentyűparancsait ismerteti. A billentyűparancsok és a menü megnyitása után a menü billentyűparancsait (a menü aláhúzott kulcsai) használhatja. A menüsávtal kapcsolatos további információkért nyissa meg a [menüsort](#menu-bar).

| Helyi | Eredmény | Menü billentyűparancsa | Eredmény |
|:--- |:--- |:--- |:--- |
| ALT + F |A **fájl** menü megnyitása. |N |Egy új konzol példányának megnyitása. |
|  |O |Megnyitja a **felügyeleti eszközök** lapot. | |
|  |S |A StorSimple Snapshot Manager konzoljának mentése. | |
|  |A |Megnyitja a **Mentés másként** lapot. | |
|  |M |Megnyitja a **beépülő modul hozzáadása/eltávolítása** lapot. | |
|  |P |Megnyitja a **Beállítások** lapot. | |
|  |H |Az online súgó megnyitása. | |
| ALT + A |A **művelet** menü megnyitása. |I |Be-és kikapcsolja az importálás megjelenítési beállítását. |
|  |W |Új StorSimple Snapshot Manager-konzol megnyitása. | |
|  |F |Frissíti a StorSimple Snapshot Manager-konzolt. | |
|  |L |Megnyitja az **exportálási lista** lapot. | |
|  |H |Az online súgó megnyitása. | |
| ALT + V |Megnyitja a **nézet** menüt. |A |Megnyitja az **Oszlopok hozzáadása/eltávolítása** lapot. |
|  |U |Megnyitja a **nézet testreszabása** lapot. | |
| ALT+O |A **Kedvencek** menü megnyitása. |A |Megnyitja a **Hozzáadás a kedvencekhez** lapot. |
|  |O |Megnyitja a **Kedvencek rendszerezése** lapot. | |
| ALT + W |Megnyitja a **ablak** menüt. |N |Egy másik StorSimple Snapshot Manager ablak megnyitása. |
|  |C |Megjeleníti az összes megnyitott konzol ablakát egy lépcsőzetes stílusban. | |
|  |T |Megjeleníti az összes megnyitott konzolos ablakot egy rácsos mintában. | |
|  |I |A képernyő alján található vízszintes sorba rendezi az ikonokat. | |
| ALT + H |A **Súgó** menü megnyitása. |H |Az online súgó megnyitása. |
|  |T |Megnyitja a Microsoft TechNet Tech Center weboldalát. | |
|  |A |Megnyitja a **Microsoft Management Console névjegye** lapot. | |

### <a name="scope-pane-shortcut-keys"></a>A hatókör ablaktáblájának gyorsbillentyűi
A következő táblázatok a **hatókör** panel egyes csomópontjainak billentyűparancsait mutatják be. 

* [Eszközök csomópontjának billentyűparancsai](#devices-node-shortcut-keys)
* [Kötetek csomópontjának gyorsbillentyűi](#volumes-node-shortcut-keys)
* [Kötetek csoportjai csomópont billentyűparancsai](#volume-groups-node-shortcut-keys)
* [Biztonsági mentési szabályzatok csomópontjának billentyűparancsai](#backup-policies-node-shortcut-keys)
* [Biztonságimásolat-katalógus csomópontjának billentyűparancsai](#backup-catalog-node-shortcut-keys)
* [Feladatok csomópontjának billentyűparancsai](#jobs-node-shortcut-keys)

#### <a name="devices-node-shortcut-keys"></a>Eszközök csomópontjának billentyűparancsai
| Helyi menü | Eredmény |
|:--- |:--- |
| C |Megnyitja az **eszköz konfigurálása** lapot. |
| T |Frissíti az eszközök és az eszköz adatainak listáját. |
| V |Megnyitja a **nézet** menüt. |
| W |Új StorSimple Snapshot Manager-konzol megnyitása a **részletek** csomópontra összpontosítva. |
| F |Frissíti a StorSimple Snapshot Manager-konzolt. |
| L |Megnyitja az **exportálási lista** lapot. |
| H |Az online súgó megnyitása. |

#### <a name="volumes-node-shortcut-keys"></a>Kötetek csomópontjának gyorsbillentyűi
| Helyi menü | Eredmény |
|:--- |:--- |
| V |Frissíti a kötetek listáját. |
| V (kétszer is megnyomva) |Megnyitja a **nézet** menüt. |
| W |Új StorSimple Snapshot Manager konzol megnyitása a **kötetek** csomópontra összpontosítva. |
| F |Frissíti a StorSimple Snapshot Manager-konzolt. |
| L |Megnyitja az **exportálási lista** lapot. |
| H |Az online súgó megnyitása. |

#### <a name="volume-groups-node-shortcut-keys"></a>Kötetek csoportjai csomópont billentyűparancsai
| Helyi menü | Eredmény |
|:--- |:--- |
| G |Megnyitja a **kötet csoport létrehozása** lapot. |
| V |Megnyitja a **nézet** menüt. |
| W |Egy új StorSimple nyit meg Snapshot Manager konzolon, amely a **mennyiségi csoportok** csomópontra koncentrál. |
| F |Frissíti a StorSimple Snapshot Manager-konzolt. |
| L |Megnyitja az **exportálási lista** lapot. |
| H |Az online súgó megnyitása. |

#### <a name="backup-policies-node-shortcut-keys"></a>Biztonsági mentési szabályzatok csomópontjának billentyűparancsai
| Helyi menü | Eredmény |
|:--- |:--- |
| B |A **házirend létrehozása** lap megnyitása. |
| V |Megnyitja a **nézet** menüt. |
| W |Egy új StorSimple nyit meg Snapshot Manager konzolon, amely a **mennyiségi csoportok** csomópontra koncentrál. |
| F |Frissíti a StorSimple Snapshot Manager-konzolt. |
| L |Megnyitja az **exportálási lista** lapot. |
| H |Az online súgó megnyitása. |

#### <a name="backup-catalog-node-shortcut-keys"></a>Biztonságimásolat-katalógus csomópontjának billentyűparancsai
| Helyi menü | Eredmény |
|:--- |:--- |
| W |Egy új StorSimple nyit meg Snapshot Manager konzolon, amely a **mennyiségi csoportok** csomópontra koncentrál. |
| F |Frissíti a StorSimple Snapshot Manager-konzolt. |
| H |Az online súgó megnyitása. |

#### <a name="jobs-node-shortcut-keys"></a>Feladatok csomópontjának billentyűparancsai
| Helyi menü | Eredmény |
|:--- |:--- |
| V |Megnyitja a **nézet** menüt. |
| W |Új StorSimple Snapshot Manager-konzol megnyitása a **feladatok** csomópontra összpontosítva. |
| F |Frissíti a StorSimple Snapshot Manager-konzolt. |
| L |Megnyitja az **exportálási lista** lapot. |
| H |Online súgó megnyitása |

## <a name="next-steps"></a>Következő lépések
* Ismerje meg, hogyan kezelheti [a StorSimple-megoldást a StorSimple Snapshot Manager használatával](storsimple-snapshot-manager-admin.md).
* Ismerje meg, hogy miként [használhatók az StorSimple Snapshot Manager az eszközök csatlakoztatásához és kezeléséhez](storsimple-snapshot-manager-manage-devices.md).