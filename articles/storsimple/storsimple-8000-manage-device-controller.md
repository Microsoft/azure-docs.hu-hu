---
title: StorSimple 8000 sorozatú eszközök vezérlőinek kezelése | Microsoft Docs
description: Megtudhatja, hogyan állíthatja le, indíthatja újra, állíthatja le vagy állíthatja alaphelyzetbe a StorSimple-eszközillesztőket.
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
ms.date: 06/19/2017
ms.author: alkohli
ms.openlocfilehash: 090797549cc61aa27945114e5ef8b666226b66e2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94956993"
---
# <a name="manage-your-storsimple-device-controllers"></a>StorSimple-eszközök vezérlőinek kezelése

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag ismerteti a StorSimple-vezérlőkön végrehajtható különböző műveleteket. A StorSimple-eszközben lévő vezérlők redundáns (társ-) vezérlők egy aktív-passzív konfigurációban. Egy adott időpontban csak egy vezérlő aktív, és az összes lemezt és hálózati műveletet feldolgozza. A másik vezérlő passzív üzemmódban van. Ha az aktív vezérlő meghibásodik, a passzív vezérlő automatikusan aktívvá válik.

Ez az oktatóanyag részletes útmutatást nyújt az eszközök vezérlőinek kezeléséhez a használatával:

* Az eszközhöz tartozó **vezérlők** panelt a StorSimple Eszközkezelő szolgáltatásban.
* Windows PowerShell StorSimple-bővítménye.

Javasoljuk, hogy az StorSimple Eszközkezelő szolgáltatáson keresztül kezelhesse az eszközöket. Ha egy műveletet csak Windows PowerShell StorSimple-bővítménye használatával lehet elvégezni, az oktatóanyag jegyezze fel.

Az oktatóanyag elolvasása után a következőket teheti:

* StorSimple-vezérlő újraindítása vagy leállítása
* StorSimple-eszköz leállítása
* A StorSimple-eszköz visszaállítása a gyári beállításokra

## <a name="restart-or-shut-down-a-single-controller"></a>Egyetlen vezérlő újraindítása vagy leállítása
Egy vezérlő újraindítása vagy leállítása nem szükséges a normál rendszerbeli művelet részeként. Egy eszköz-vezérlő leállítási művelete csak olyan esetekben gyakori, amikor egy hibás eszköz hardver-összetevőjének cseréje szükséges. A vezérlő újraindítása olyan helyzetben is megkövetelhető, amelyben a túlzott memóriahasználat vagy az üzemzavart okozó vezérlő befolyásolja a teljesítményt. Előfordulhat, hogy a vezérlő cseréjét követően újra kell indítania a vezérlőt, ha engedélyezni és tesztelni szeretné a lecserélt vezérlőt.

Az eszköz újraindítása nem okoz zavart a csatlakoztatott kezdeményezők működésében, feltéve, hogy a passzív vezérlő elérhető. Ha a passzív vezérlő nem érhető el vagy ki van kapcsolva, az aktív vezérlő újraindítása a szolgáltatás megszakadását és leállást eredményezhet.

> [!IMPORTANT]
> * **A futó vezérlőt soha nem szabad fizikailag eltávolítani, mivel ez a redundancia elvesztését eredményezi, és nagyobb a leállás kockázata.**
> * Az alábbi eljárás csak a StorSimple fizikai eszközre vonatkozik. További információ a StorSimple Cloud Appliance indításáról, leállításáról és újraindításáról: [a felhőalapú berendezés használata](storsimple-8000-cloud-appliance-u2.md#work-with-the-storsimple-cloud-appliance).

A StorSimple Eszközkezelő szolgáltatás vagy a Windows PowerShell StorSimple-bővítménye Azure Portal használatával újraindíthatja vagy leállíthatja az egyes eszközök vezérlőit.

Az Azure Portal a következő lépések végrehajtásával felügyelheti az eszközillesztőket.

#### <a name="to-restart-or-shut-down-a-controller-in-azure-portal"></a>Vezérlő újraindítása vagy leállítása Azure Portal
1. A StorSimple Eszközkezelő szolgáltatásban válassza az **eszközök** lehetőséget. Válassza ki az eszközt az eszközök listájából. 

    ![Válasszon egy eszközt.](./media/storsimple-8000-manage-device-controller/manage-controller1.png)

2. Lépjen a **beállítások > vezérlők menüpontra**.
   
    ![A StorSimple-vezérlők kifogástalan állapotának ellenőrzése](./media/storsimple-8000-manage-device-controller/manage-controller2.png)
3. A **vezérlők** panelen ellenőrizze, hogy az eszközön lévő vezérlők állapota is **kifogástalan**-e. Válasszon ki egy vezérlőt, kattintson a jobb gombbal, majd válassza az **Újraindítás** vagy a **Leállítás** lehetőséget.

    ![StorSimple-vezérlők újraindítása vagy leállítása](./media/storsimple-8000-manage-device-controller/manage-controller3.png)

4. Létre kell hozni egy feladatot a vezérlő újraindításához vagy leállításához, és a megfelelő figyelmeztetések jelennek meg, ha vannak ilyenek. Az újraindítás vagy a Leállítás figyeléséhez nyissa meg a **szolgáltatás > tevékenység naplóit** , és szűrje a szolgáltatásra jellemző paraméterek alapján. Ha a vezérlőt leállították, a bekapcsoláshoz be kell állítania a főkapcsolót.

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Vezérlő újraindítása vagy leállítása Windows PowerShell StorSimple-bővítménye
A következő lépések végrehajtásával állítsa le vagy indítsa újra a StorSimple-eszköz egyetlen vezérlőjét a Windows PowerShell StorSimple-bővítménye.

1. Az eszközt a soros konzolon vagy egy távoli számítógépről származó Telnet-munkameneten keresztül érheti el. A 0 vagy a Controller 1 vezérlőhöz való kapcsolódáshoz kövesse a [Putty használata az eszköz soros konzolhoz való kapcsolódáshoz](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)című szakasz lépéseit.
2. A soros konzol menüjében válassza az 1. lehetőséget, majd **Jelentkezzen be a teljes hozzáférés** lehetőséggel.
3. A szalagcím üzenetben jegyezze fel azt a vezérlőt, amelyhez csatlakozik (0. vezérlő vagy vezérlő 1), és hogy az aktív vagy a passzív (készenléti) vezérlő.
   
   * Egyetlen vezérlő leállításához a parancssorba írja be a következőt:
     
       `Stop-HcsController`
     
       Ezzel leállítja azt a vezérlőt, amelyhez csatlakozik. Ha leállítja az aktív vezérlőt, az eszköz feladatátvételt hajt végre a passzív vezérlőn.

   * A vezérlő újraindításához írja be a következőt a parancssorba:
     
       `Restart-HcsController`
     
       Ezzel újraindítja azt a vezérlőt, amelyhez csatlakozik. Ha újraindítja az aktív vezérlőt, az újraindítást megelőzően feladatátvételt hajt végre a passzív vezérlőn.

## <a name="shut-down-a-storsimple-device"></a>StorSimple-eszköz leállítása

Ez a szakasz azt ismerteti, hogyan lehet leállítani egy futó vagy sikertelen StorSimple eszközt egy távoli számítógépről. Az eszköz ki van kapcsolva, miután az eszközillesztők le vannak állítva. Az eszköz leállítása az eszköz fizikai áthelyezésekor vagy a szolgáltatáson kívül történik.

> [!IMPORTANT]
> Az eszköz leállítása előtt tekintse meg az eszköz összetevőinek állapotát. Keresse meg az eszközt, majd kattintson a **beállítások > hardver állapota** lehetőségre. Az **állapot és hardver** állapota panelen ellenőrizze, hogy az összes összetevő LED állapota zöld. Csak egy kifogástalan állapotban lévő eszköz rendelkezik zöld állapottal. Ha az eszköz leállítása hibás összetevő helyett történik, akkor a megfelelő összetevő (k) esetében a sikertelen (piros) vagy a csökkentett teljesítményű (sárga) állapot jelenik meg.


#### <a name="to-shut-down-a-storsimple-device"></a>StorSimple-eszköz leállítása

1. Az eszközön a passzív vezérlő azonosításához vagy leállításához használja a [vezérlő újraindítása vagy](#restart-or-shut-down-a-single-controller) leállítása műveletet. Ezt a műveletet a Azure Portal vagy Windows PowerShell StorSimple-bővítménye is végrehajthatja.
2. Az aktív vezérlő leállításához ismételje meg a fenti lépést.
3. Ekkor meg kell vizsgálnia az eszköz hátsó síkjait. A két vezérlő teljes leállítása után mindkét vezérlőn a LED-ek állapotát piros színnel kell ellátni. Ha most ki kell kapcsolni az eszközt, a Power és a hűtési modulok (PCMs-EK) kikapcsolt állapotára kell váltania. Ez kikapcsolja az eszközt.

## <a name="reset-the-device-to-factory-default-settings"></a>Az eszköz visszaállítása a gyári alapértelmezett beállításokra

> [!IMPORTANT]
> Ha alaphelyzetbe kell állítania az eszközt az alapértelmezett gyári beállításokra, forduljon a Microsoft ügyfélszolgálatahoz. Az alább ismertetett eljárást csak Microsoft ügyfélszolgálatasal együtt kell használni.

Ez az eljárás leírja, hogyan állíthatja alaphelyzetbe a Microsoft Azure StorSimple eszközt a gyári alapértelmezett beállításokra Windows PowerShell StorSimple-bővítménye használatával.
Az eszköz alaphelyzetbe állítása alapértelmezés szerint eltávolítja a teljes fürt összes adatait és beállítását.

A következő lépések végrehajtásával alaphelyzetbe állíthatja Microsoft Azure StorSimple eszközét az alapértelmezett gyári beállításokra:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Az eszköz alaphelyzetbe állítása az alapértelmezett beállításokkal Windows PowerShell StorSimple-bővítménye
1. Az eszközt a soros konzolján keresztül érheti el. Ellenőrizze a szalagcím üzenetét, és győződjön meg arról, hogy csatlakozik az **aktív** vezérlőhöz.
2. A soros konzol menüjében válassza az 1. lehetőséget, majd **Jelentkezzen be a teljes hozzáférés** lehetőséggel.
3. A parancssorba írja be a következő parancsot a teljes fürt alaphelyzetbe állításához, az összes adat, a metaadatok és a vezérlő beállításainak eltávolításával:
   
    `Reset-HcsFactoryDefault`
   
    Egy vezérlő visszaállításához használja a  [reset-HcsFactoryDefault](/previous-versions/windows/powershell-scripting/dn688132(v=wps.630)) parancsmagot a `-scope` paraméterrel.)
   
    A rendszer többször is újraindul. A rendszer értesítést küld, ha az Alaphelyzetbe állítás sikeresen befejeződött. A rendszermodelltől függően 45-60 percet is igénybe vehet egy 8100-es eszközön és 60-90 percen belül egy 8600-re a folyamat befejezéséhez.
   
## <a name="questions-and-answers-about-managing-device-controllers"></a>Az eszközök vezérlőinek kezelésével kapcsolatos kérdések és válaszok
Ebben a szakaszban összefoglaljuk a StorSimple-vezérlők kezelésével kapcsolatos gyakori kérdéseket.

**Q.** Mi történik, ha az eszközön lévő vezérlők állapota Kifogástalan, és be van kapcsolva, és újraindulok vagy leállítottam az aktív vezérlőt?

**Egy.** Ha az eszközön lévő vezérlők állapota Kifogástalan, és be van kapcsolva, a rendszer megerősítést kér. A következőket dönthet:

* **Indítsa újra az aktív vezérlőt** – értesítést kap arról, hogy egy aktív vezérlő újraindítása miatt az eszköz feladatátvételt okozott a passzív vezérlőnek. A vezérlő újraindul.
* **Aktív vezérlő leállítása** – értesítést kap arról, hogy az aktív vezérlő leállítása leállást eredményez. A vezérlő bekapcsolásához le kell küldenie az eszköz főkapcsoló gombját is.

**Q.** Mi történik, ha az eszközön található passzív vezérlő nem érhető el vagy ki van kapcsolva, és újraindulok vagy leállítottam az aktív vezérlőt?

**Egy.** Ha az eszközön található passzív vezérlő nem érhető el vagy ki van kapcsolva, és a következőket választja:

* **Indítsa újra az aktív vezérlőt** – értesítést kap arról, hogy a művelet folytatása a szolgáltatás átmeneti megszakadását eredményezi, és a rendszer megerősítést kér.
* **Aktív vezérlő leállítása** – a rendszer értesítést küld a művelet folytatásáról. Az eszköz bekapcsolásához le kell küldenie az egyik vagy mindkét vezérlő főkapcsoló gombját is. A rendszer megerősítést kér.

**Q.** Mikor nem sikerül a vezérlő újraindítása vagy leállítása?

**Egy.** A vezérlő újraindítása vagy leállítása sikertelen lehet, ha:

* Folyamatban van egy eszköz frissítése.
* Már folyamatban van egy vezérlő újraindítása.
* Már folyamatban van egy vezérlő leállítása.

**Q.** Hogyan állapítható meg, hogy a rendszer újraindította vagy leállította-e a vezérlőt?

**Egy.** A vezérlő állapotát a vezérlő panelen tekintheti meg. A vezérlő állapota jelzi, hogy a vezérlő újraindítási vagy leállítási folyamatban van-e. Emellett a **riasztások** panel tájékoztató riasztást is tartalmaz, ha a vezérlő újraindul vagy leáll. A vezérlő újraindítási és leállítási műveleteit a tevékenység naplófájljai is rögzítik. A tevékenységek naplóival kapcsolatos további információkért [tekintse meg a tevékenységek naplóinak megtekintése](storsimple-8000-service-dashboard.md#view-the-activity-logs)című témakört.

**Q.** Van hatással az I/O-re a vezérlő feladatátvételének eredményeképpen?

**Egy.** A kezdeményezők és az aktív vezérlő közötti TCP-kapcsolatok alaphelyzetbe állnak a vezérlő feladatátvételének eredményeképpen, de a rendszer újra létrejön, amikor a passzív vezérlő feltételezi a műveletet. Előfordulhat, hogy a művelet során a kezdeményezők és az eszköz között egy ideiglenes (30 másodpercnél rövidebb) szünet történik.

**Q.** Hogyan visszaküldi a vezérlőt a szolgáltatásnak a leállítása és eltávolítása után?

**Egy.** Ahhoz, hogy egy vezérlőt vissza lehessen adni a szolgáltatásnak, be kell szúrnia azt a [StorSimple-eszköz vezérlő-moduljának cseréje](storsimple-8000-controller-replacement.md)című részében leírtak szerint.

## <a name="next-steps"></a>Következő lépések
* Ha olyan problémák merülnek fel a StorSimple-vezérlőkben, amelyeket az oktatóanyagban felsorolt eljárások használatával nem tud feloldani, [forduljon Microsoft ügyfélszolgálatahoz](storsimple-8000-contact-microsoft-support.md).
* Ha többet szeretne megtudni a StorSimple Eszközkezelő szolgáltatás használatáról, látogasson el [a StorSimple Eszközkezelő szolgáltatás használatára a StorSimple-eszköz felügyeletéhez](storsimple-8000-manager-service-administration.md).