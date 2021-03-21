---
title: StorSimple virtuális tömbhöz tartozó riasztások megtekintése és kezelése
description: Ismerteti a StorSimple virtuális tömb riasztási feltételeit és súlyosságát, valamint azt, hogy miként használható a StorSimple Manager szolgáltatás a riasztások kezeléséhez.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 97ee25a1-0ec3-4883-9a0a-54b722598462
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/12/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 12fcc9996697f3bbba35826d79bec238bfb0f8b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95993012"
---
# <a name="use-storsimple-device-manager-to-manage-alerts-for-the-storsimple-virtual-array"></a>A StorSimple Eszközkezelő használata a StorSimple virtuális tömbhöz tartozó riasztások kezelésére

## <a name="overview"></a>Áttekintés

A StorSimple Eszközkezelő szolgáltatás riasztások funkciója lehetővé teszi a valós idejű StorSimple virtuális tömbökkel kapcsolatos riasztások áttekintését és törlését. A **Szolgáltatások összegzése** panelen a riasztások segítségével központilag figyelheti a StorSimple virtuális tömbök állapotát és a teljes Microsoft Azure StorSimple megoldást.

Ez az oktatóanyag leírja, hogyan konfigurálhatja a riasztási értesítéseket, a gyakori riasztási feltételeket, a riasztás súlyossági szintjét, valamint a riasztások megtekintését és nyomon követését. Emellett riasztási gyors referenciával rendelkező táblákat is tartalmaz, amelyek lehetővé teszik, hogy gyorsan megkeresse egy adott riasztást, és megfelelően reagáljon.

![Riasztások lap](./media/storsimple-virtual-array-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>Riasztási beállítások konfigurálása

Megadhatja, hogy szeretne-e e-mailben értesítést kapni az egyes StorSimple virtuális tömbök riasztási feltételeiről. Emellett a riasztási értesítés címzettjeit is azonosíthatja, ha az e-mail-címeit a **további e-mail címzettek** mezőben adja meg, pontosvesszővel elválasztva.

> [!NOTE]
> Virtuális tömbként legfeljebb 20 e-mail-címet adhat meg.

Miután engedélyezte az e-mailes értesítést egy virtuális tömbhöz, az értesítési lista tagjai minden alkalommal kapnak egy e-mail-üzenetet, amikor kritikus riasztás történik. Az üzenetek a *storsimple-Alerts-reply \@ mail.windowsazure.com* lesznek elküldve, és leírják a riasztási feltételt. A címzettek a **leiratkozás** lehetőségre kattintva eltávolíthatják magukat az e-mail értesítések listájáról.

#### <a name="to-enable-email-notification-for-alerts"></a>E-mail-értesítések engedélyezése a riasztásokhoz

1. Lépjen a StorSimple Eszközkezelő szolgáltatásra, és a **felügyelet** szakaszban válassza ki az **eszközök** elemet. A megjelenő eszközök listájából válassza ki az eszközt, majd kattintson rá.
   
    ![riasztási beállítások](./media/storsimple-virtual-array-manage-alerts/alerts2.png)
2. Ekkor megnyílik a **Beállítások** panel. Az **eszközbeállítások** szakaszban válassza az **általános** lehetőséget. Ekkor megnyílik az **általános beállítások** panel.
   
    ![A képernyőképen az eszközbeállítások panel jelenik meg a riasztási beállítások területen.](./media/storsimple-virtual-array-manage-alerts/alerts4.png)
3. Az **általános beállítások** panelen lépjen a **riasztási beállítások** szakaszra, és állítsa be a következőket:
   
   1. Az **e-mail értesítés engedélyezése** mezőben válassza az **Igen** lehetőséget.
   2. Az **e-mail szolgáltatás-rendszergazdák** mezőben válassza az **Igen** lehetőséget, ha szeretné, hogy a szolgáltatás rendszergazdája és az összes társ-rendszergazda megkapja a riasztási értesítéseket.
   3. A **további e-mail-címzettek** mezőben adja meg az összes többi címzett e-mail-címét, akik számára meg kell kapnia a riasztási értesítéseket. Adja meg a neveket a *valaki \@ Somewhere.com* formátumban. Az e-mail-címeket pontosvesszővel válassza el egymástól. Virtuális eszközökön legfeljebb 20 e-mail-címet állíthat be.
      
       ![A képernyőkép a riasztási beállítások részleteit jeleníti meg az ebben a lépésben ismertetett beállításokkal.](./media/storsimple-virtual-array-manage-alerts/alerts6.png)
   4. Teszt e-mail értesítés küldéséhez kattintson a **Tesztüzenet küldése** elemre. A StorSimple Eszközkezelő szolgáltatás megjeleníti az állapotüzenetek üzeneteit, mert továbbítja a teszt értesítését.
      
       ![A képernyőképen egy tájékoztató párbeszédpanel jelenik meg, amely ellenőrzi a teszt e-mail-címét.](./media/storsimple-virtual-array-manage-alerts/alerts7.png)
      
      > [!NOTE]
      > Ha a teszt értesítési üzenet nem küldhető el, a StorSimple Eszközkezelő szolgáltatás megjeleníti a megfelelő üzenetet. Kattintson **az OK** gombra, várjon néhány percet, majd próbálja meg újra elküldeni a teszt értesítési üzenetet.
      >
      >
   5. A konfiguráció mentéséhez kattintson a lap alján található **Mentés** gombra. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra.
      
      ![Képernyőfelvétel: a beállítások ablaktábla, amelyen a Mentés gomb van kiválasztva.](./media/storsimple-virtual-array-manage-alerts/alerts10.png)

## <a name="common-alert-conditions"></a>Gyakori riasztási feltételek

A StorSimple Virtual Array számos feltételre reagálva riasztásokat hoz létre. A riasztási feltételek leggyakoribb típusai a következők:

* **Kapcsolódási problémák** – ezek a riasztások akkor fordulnak elő, ha az adatátviteli nehézségek merülnek fel. Kommunikációs problémák léphetnek fel az Azure Storage-fiókba irányuló és onnan érkező adatok átvitele során, vagy a virtuális eszközök és a StorSimple Eszközkezelő szolgáltatás közötti kapcsolat hiánya miatt. A kommunikációs problémák némelyike a legnehezebb megoldás, mert sok meghibásodási pont van. Mindig először ellenőrizze, hogy a hálózati kapcsolat és az Internet-hozzáférés elérhető-e, mielőtt továbblépne a fejlettebb hibaelhárításra. A portok és a tűzfal beállításairól a [StorSimple virtuális tömb rendszerkövetelményei](storsimple-ova-system-requirements.md)című témakörben olvashat bővebben. A hibaelhárítással kapcsolatos segítségért keresse fel a következőt: [Hibaelhárítás a Test-Connection parancsmaggal](./storsimple-8000-troubleshoot-deployment.md).
* **Teljesítménnyel kapcsolatos problémák** – ezek a riasztások akkor keletkeznek, ha a rendszer nem optimális teljesítményű, például ha nagy terhelés alatt áll.

Emellett előfordulhat, hogy a biztonsággal, a frissítésekkel vagy a feladatok meghibásodásával kapcsolatos riasztások jelennek meg.

## <a name="alert-severity-levels"></a>Riasztás súlyossági szintjei

A riasztások eltérő súlyossági szinttel bírnak, attól függően, hogy milyen hatással van a riasztási helyzetre, és hogy a riasztásra adott válaszra van szükség. A súlyossági szintek a következőek:

* **Kritikus** – ez a riasztás olyan feltételre reagál, amely hatással van a rendszer sikeres teljesítményére. A művelet végrehajtásához szükséges, hogy a StorSimple szolgáltatás ne legyen megszakítva.
* **Figyelmeztetés** – ez az állapot kritikus lehet, ha nincs megoldva. Meg kell vizsgálnia a helyzetet, és el kell végeznie a probléma törléséhez szükséges lépéseket.
* **Információ** – ez a riasztás olyan információkat tartalmaz, amelyek hasznosak lehetnek a rendszer nyomon követésében és kezelésében.

## <a name="view-and-track-alerts"></a>Riasztások megtekintése és nyomon követése

A StorSimple Eszközkezelő szolgáltatások összegzése panel gyors áttekintést nyújt a virtuális eszközökön lévő riasztások számáról, súlyossági szint szerint rendezve.

![Riasztások irányítópult](./media/storsimple-virtual-array-manage-alerts/alerts14.png)

Kattintson a súlyossági szintre a **riasztások** panel megnyitásához. Az eredmények csak azokat a riasztásokat tartalmazzák, amelyek megfelelnek a súlyossági szintnek.

![Riasztási típusú riasztási jelentés – hatókör](./media/storsimple-virtual-array-manage-alerts/alerts15.png)

Kattintson egy riasztásra a listában, hogy további részleteket kapjon a riasztásról, beleértve a riasztás legutóbbi jelentésének időpontját, az eszközön előforduló riasztások számát, valamint a riasztás feloldásához javasolt műveletet.

![Riasztások listája és részletei](./media/storsimple-virtual-array-manage-alerts/alerts16.png)

Ha az adatokat Microsoft ügyfélszolgálata szeretné elküldeni, a riasztás részleteit szövegfájlba másolhatja. Miután követte a javaslatot, és feloldotta a riasztási feltételt a helyszínen, törölje a riasztást a listáról. Válassza ki a riasztást a listából, majd kattintson a **Törlés** gombra. Több riasztás törléséhez jelölje ki az egyes riasztásokat, kattintson bármelyik oszlopra a **riasztási** oszlop kivételével, majd kattintson a **Törlés** gombra, miután kiválasztotta az összes riasztást, amelyeket törölni szeretne.

Ha a **Törlés** gombra kattint, lehetősége lesz a riasztással kapcsolatos megjegyzések és a probléma megoldásához szükséges lépések megadására.

![riasztási megjegyzések](./media/storsimple-virtual-array-manage-alerts/alerts17.png)

A rendszer néhány eseményt töröl, ha egy másik esemény új információval aktiválódik.

## <a name="sort-and-review-alerts"></a>Riasztások rendezése és áttekintése

A **riasztások** panel legfeljebb 250 riasztást tud megjeleníteni. Ha túllépte a riasztások számát, nem minden riasztás jelenik meg az alapértelmezett nézetben. A következő mezőket kombinálva testreszabhatja, hogy mely riasztások jelenjenek meg:

* **Állapot** – **aktív** vagy **törölt** riasztásokat is megjeleníthet. Az aktív riasztások még mindig aktiválva vannak a rendszeren, míg a törölt riasztásokat a rendszergazda manuálisan törölte vagy programozott módon törölte, mert a rendszer frissítette a riasztási feltételt új információkkal.
* **Súlyosság** – riasztásokat jeleníthet meg az összes súlyossági szintről (kritikus, figyelmeztetés, információ), vagy csak egy bizonyos súlyosságot, például csak a kritikus riasztásokat.
* **Forrás** – megjelenítheti az összes forrásból származó riasztásokat, vagy korlátozhatja a riasztásokat a szolgáltatásból vagy az egyikből vagy az összes virtuális eszközből érkezők számára.
* **Időtartomány** – a **kezdő** és **a** dátumok és időbélyegek megadásával megtekintheti a riasztásokat az Ön által érintett időszakban.

## <a name="alerts-quick-reference"></a>Riasztások – rövid útmutató

Az alábbi táblázatok felsorolják az esetlegesen felmerülő StorSimple-riasztásokat, valamint a további információkat és javaslatokat, ahol elérhetők. A StorSimple Virtual Array-riasztások a következő kategóriák valamelyikébe sorolhatók:

* [Felhőalapú csatlakozási riasztások](#cloud-connectivity-alerts)
* [Konfigurációs riasztások](#configuration-alerts)
* [Sikertelen feladatok riasztásai](#job-failure-alerts)
* [Teljesítmény-riasztások](#performance-alerts)
* [Biztonsági riasztások](#security-alerts)

### <a name="cloud-connectivity-alerts"></a>Felhőalapú csatlakozási riasztások

| Riasztás szövege | Esemény | További információ/javasolt műveletek |
|:--- |:--- |:--- |
| Az eszköz <*eszköz neve*> nincs csatlakoztatva a felhőhöz. |Az elnevezett eszköz nem tud csatlakozni a felhőhöz. |Nem lehet csatlakozni a felhőhöz. Ezt a következők egyike okozhatja:<ul><li>Probléma merülhet fel az eszköz hálózati beállításaival kapcsolatban.</li><li>Probléma lehet a Storage-fiók hitelesítő adataival.</li></ul>A kapcsolódási problémák elhárításával kapcsolatos további információkért nyissa meg az eszköz [helyi webes felületét](storsimple-ova-web-ui-admin.md) . |

### <a name="configuration-alerts"></a>Konfigurációs riasztások

| Riasztás szövege | Esemény | További információ/javasolt műveletek |
|:--- |:--- |:--- |
| A helyszíni virtuális eszköz konfigurációja nem támogatott. |Lassú teljesítmény. |A jelenlegi konfiguráció a teljesítmény romlását okozhatja. Győződjön meg arról, hogy a kiszolgáló megfelel a minimális konfigurációs követelményeknek. További információkért keresse fel a [StorSimple virtuális tömbre vonatkozó követelményeket](storsimple-ova-system-requirements.md). |
| Elfogyott a kiépített lemezterület a <*eszköz neve* területen \> . |Lemezterület-figyelmeztetés. |Kevés a kiosztott lemezterület. Lemezterület felszabadításához érdemes lehet áthelyezni a számítási feladatokat egy másik kötetre, vagy az adatok megosztására vagy törlésére. |

### <a name="job-failure-alerts"></a>Sikertelen feladatok riasztásai

| Riasztás szövege | Esemény | További információ/javasolt műveletek |
|:--- |:--- |:--- |
| Nem sikerült a <*eszköz nevének* biztonsági mentése \> . |A biztonsági mentési feladatok sikertelenek. |Nem hozható létre biztonsági másolat. Vegye figyelembe a következők egyikét:<ul><li>A kapcsolódási problémák megakadályozhatják a biztonsági mentési művelet sikeres befejezését. Győződjön meg arról, hogy nincsenek kapcsolódási problémák. A kapcsolódási problémák elhárításával kapcsolatos további információkért nyissa meg a virtuális eszköz [helyi webes felületét](storsimple-ova-web-ui-admin.md) .</li><li>Elérte a rendelkezésre álló tárterület korlátját. Lemezterület felszabadításához érdemes lehet törölni a már nem szükséges biztonsági másolatokat.</li></ul> Hárítsa el a problémákat, törölje a riasztást, majd próbálja megismételni a műveletet. |
| Nem sikerült befejezni a <*eszköznév* klónozását \> . |Sikertelen klónozási feladatok. |Nem hozható létre klón. Vegye figyelembe a következők egyikét:<ul><li>Lehetséges, hogy a biztonsági mentési lista nem érvényes. Frissítse a listát annak ellenőrzéséhez, hogy továbbra is érvényes-e.</li><li>A kapcsolódási problémák megakadályozhatják a klónozási művelet sikeres befejezését. Győződjön meg arról, hogy nincsenek kapcsolódási problémák.</li><li>Elérte a rendelkezésre álló tárterület korlátját. Lemezterület felszabadításához érdemes lehet törölni a már nem szükséges biztonsági másolatokat.</li></ul>Hárítsa el a problémákat, törölje a riasztást, majd próbálja megismételni a műveletet. |

### <a name="networking-alerts"></a>Hálózati riasztások

| Riasztás szövege | Esemény | További információ/javasolt műveletek |
|:--- |:--- |:--- |
| Nem lehet csatlakozni a hitelesítési szolgáltatáshoz. |DataPath hiba |A hitelesítéshez használt URL-cím nem érhető el. Győződjön meg arról, hogy a tűzfalszabályok tartalmazzák a StorSimple-eszközhöz megadott URL-mintákat. A Azure Portal URL-mintázatával kapcsolatos további információkért nyissa meg a [StorSimple virtuális tömb hálózati követelményeit](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).|

### <a name="performance-alerts"></a>Teljesítmény-riasztások

| Riasztás szövege | Esemény | További információ/javasolt műveletek |
|:--- |:--- |:--- |
| Váratlan késéseket tapasztal az adatátvitel során. |Lassú adatátvitel. |Sávszélesség-szabályozási hibák léphetnek fel, ha túllépi a tárolási szolgáltatás skálázhatósági céljait. A Storage szolgáltatás ezzel biztosítja, hogy egyetlen ügyfél vagy bérlő sem használhatja a szolgáltatást mások rovására. Az Azure Storage-fiók hibaelhárításával kapcsolatos további információkért keresse fel az [Microsoft Azure Storage figyelését, diagnosztizálását és hibaelhárítását](../storage/common/storage-monitoring-diagnosing-troubleshooting.md)ismertető témakört. |
| A <*eszköz nevében* kevés a helyi foglalási lemezterület \> . |Lassú válaszidő. |a <eszköznév teljes kiépített méretének 10%  \> -a a helyi eszközön van fenntartva, és a lefoglalt terület már alacsonyan fut. A <*eszköznév* munkaterhelése \> magasabb mértékű adatvesztést eredményez, vagy előfordulhat, hogy a közelmúltban nagy mennyiségű adat lett áttelepítve. Ez csökkentheti a teljesítményt. A probléma megoldásához vegye figyelembe a következő műveletek egyikét:<ul><li>Növelje a felhő sávszélességét erre az eszközre.</li><li>A munkaterhelések csökkentése vagy áthelyezése egy másik kötetre vagy megosztásra.</li></ul> |

### <a name="security-alerts"></a>Biztonsági riasztások

| Riasztás szövege | Esemény | További információ/javasolt műveletek |
|:--- |:--- |:--- |
| A <*eszköznév* jelszava \> <*szám* \> nap múlva lejár. |Jelszó figyelmeztetése |A jelszó <*számú* \> nap múlva lejár. Érdemes lehet módosítani a jelszavát. További információért lépjen [a StorSimple virtuális tömb eszköz rendszergazdai jelszavának módosítása](storsimple-virtual-array-change-device-admin-password.md)elemre. |

## <a name="next-steps"></a>Következő lépések

* [További információ a StorSimple virtuális tömbről](storsimple-ova-overview.md).