---
title: Feladatátvételi és vész-helyreállítási StorSimple virtuális tömb esetén
description: Ismerje meg a Microsoft Azure StorSimple virtuális tömb vész-helyreállítási feladatait, beleértve a további virtuális tömbök feladatátvételének részletes lépéseit.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 3c1f9c62-af57-4634-a0d8-435522d969aa
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6a321b2eb79ea01bbf94e1a413c189ac7f4614ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99096423"
---
# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array-via-azure-portal"></a>Vészhelyreállítás és az eszköz feladatátvétele a StorSimple Virtual Arrayhez az Azure Portalon keresztül

## <a name="overview"></a>Áttekintés
Ez a cikk a Microsoft Azure StorSimple virtuális tömb vész-helyreállítási folyamatát ismerteti, beleértve a további virtuális tömbök feladatátvételének részletes lépéseit. A feladatátvétel lehetővé teszi az adatok áthelyezését az adatközpontban lévő *forrásoldali* eszközről egy *céleszköz* . Előfordulhat, hogy a célként megadott eszköz ugyanabban vagy egy másik földrajzi helyen található. Az eszköz feladatátvétele a teljes eszközre irányul. A feladatátvétel során a forrás eszköz Felhőbeli adatai megváltoztatják a célként megadott eszköz tulajdonjogát.

Ez a cikk csak a StorSimple virtuális tömbökre vonatkozik. Az 8000 sorozatú eszközök feladatátvételéhez nyissa meg a [StorSimple eszköz feladatátvételi és vész-helyreállítási](./storsimple-8000-device-failover-disaster-recovery.md)eszközét.

## <a name="what-is-disaster-recovery-and-device-failover"></a>Mi az a vész-helyreállítás és az eszköz feladatátvétele?

Vész-helyreállítási (DR) forgatókönyv esetén az elsődleges eszköz működése leáll. Ebben a forgatókönyvben a hibás eszközhöz társított Felhőbeli adatmennyiséget át lehet helyezni egy másik eszközre. Használhatja az elsődleges eszközt *forrásként* , és megadhat egy másik eszközt *célként*. Ezt a folyamatot *feladatátvételnek* nevezzük. A feladatátvétel során a forrásként szolgáló eszköz minden kötete vagy megosztása, valamint a cél eszközre kerül át. Az Adatszűrés nem engedélyezett.

A DR teljes eszköz-visszaállításként van modellezve a Heat Map-alapú rétegek és nyomon követés használatával. A hő-Térkép úgy van meghatározva, hogy az olvasási és írási mintákon alapuló hő értéket rendel hozzájuk az adattípushoz. Ez a Heat Map ezután a legalacsonyabb hő-adattömböket először a felhőbe tömöríti, miközben a nagy teljesítményű (leggyakrabban használt) adattömböket a helyi szinten tartja. DR alatt a StorSimple a meleg térképet használja a felhőből származó adatok visszaállításához és helyreállításához. Az eszköz beolvassa az összes kötetet/megosztást az utolsó legutóbbi biztonsági mentésben (a belső meghatározás szerint), és visszaállítja a biztonsági másolatból a visszaállítást. A virtuális tömb a teljes DR folyamatot dolgozza fel.

> [!IMPORTANT]
> A rendszer törli a forrás eszközt az eszköz feladatátvételének befejezésekor, ezért a feladat-visszavétel nem támogatott.
> 
> 

A vész-helyreállítási folyamat az eszköz feladatátvételi funkciójával van elindítva, és az **eszközök** panelen indítható el. Ez a panel a StorSimple Eszközkezelő szolgáltatáshoz csatlakoztatott összes StorSimple-eszközt tabulates. Minden eszköz esetében megtekintheti a felhasználóbarát nevet, az állapotot, a kiépített és a maximális kapacitást, a típust és a modellt.

## <a name="prerequisites-for-device-failover"></a>Eszköz-feladatátvétel előfeltételei

### <a name="prerequisites"></a>Előfeltételek

Az eszközök feladatátvétele esetén győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:

* A forrás eszköznek **inaktivált** állapotban kell lennie.
* A célként megadott eszköznek készen kell lennie a Azure Portal **beállítására** . A cél virtuális tömb kiépítése azonos vagy nagyobb kapacitással. A helyi webes felhasználói felület használatával konfigurálhatja és sikeresen regisztrálhatja a célként megadott virtuális tömböt.
  
  > [!IMPORTANT]
  > Ne próbálja meg konfigurálni a regisztrált virtuális eszközt a szolgáltatáson keresztül. A szolgáltatáson keresztül nem kell végrehajtani az eszköz konfigurációját.
  > 
  > 
* A céleszköz neve nem egyezhet meg a forrásoldali eszközzel.
* A forrás és a cél eszköznek ugyanolyan típusúnak kell lennie. Csak egy fájlkiszolgáló által egy másik fájlkiszolgálón konfigurált virtuális tömb feladatátvétele hajtható végre. Ugyanez érvényes az iSCSI-kiszolgálók esetében is.
* A DR-hez készült fájlkiszolgáló esetében javasoljuk, hogy a forrással megegyező tartományhoz csatlakoztassa a célként megadott eszközt. Ez a konfiguráció biztosítja a megosztási engedélyek automatikus feloldását. Csak az azonos tartományban lévő céleszköz feladatátvétele támogatott.
* A DR számára elérhető céleszköz olyan eszközök, amelyek a forrásoldali eszközhöz képest azonos vagy nagyobb kapacitással rendelkeznek. A szolgáltatáshoz csatlakoztatott eszközök, de nem felelnek meg a megfelelő hely feltételeinek, nem érhetők el célként.

### <a name="other-considerations"></a>További szempontok

* Tervezett feladatátvétel esetén:
  
  * Javasoljuk, hogy a forrásoldali eszközön lévő összes kötetet vagy megosztást offline állapotba helyezze.
  * Javasoljuk, hogy készítsen biztonsági másolatot az eszközről, majd folytassa a feladatátvételt az adatvesztés csökkentése érdekében.
* Nem tervezett feladatátvétel esetén az eszköz a legutóbbi biztonsági mentést használja az adatvisszaállításhoz.

### <a name="device-failover-prechecks"></a>Eszközök feladatátvételének elővizsgálatai

A DR megkezdése előtt az eszköz előzetes ellenőrzést végez. Ezek az ellenőrzések segítenek biztosítani, hogy a DR megkezdéskor ne történjen hiba. Az előzetes ellenőrzések a következők:

* A Storage-fiók ellenőrzése.
* A felhőalapú kapcsolat ellenőrzése az Azure-ban.
* A megcélzott eszközön rendelkezésre álló terület ellenőrzése.
* Annak ellenőrzése, hogy az iSCSI-kiszolgáló forrásoldali eszközének kötete rendelkezik-e
  
  * érvényes ACR-nevek.
  * érvényes IQN (legfeljebb 220 karakter).
  * érvényes CHAP-jelszavak (12-16 karakter hosszúak).

Ha a fenti előzetes ellenőrzések bármelyike meghiúsul, nem folytathatja a DR-t. Oldja meg ezeket a problémákat, majd próbálkozzon újra DR.

A DR sikeres befejeződése után a rendszer átviszi a Felhőbeli adatmennyiséget az eszközön. A forrásoldali eszköz ezután már nem érhető el a portálon. A forrásoldali eszközön lévő összes kötethez/megosztáshoz való hozzáférés le van tiltva, és a céleszköz aktívvá válik.

> [!IMPORTANT]
> Bár az eszköz már nem érhető el, a gazdagép rendszeren kiépített virtuális gép továbbra is erőforrásokat használ. A DR sikeres befejezését követően törölheti a virtuális gépet a gazdagép rendszeréről.
> 
> 

## <a name="fail-over-to-a-virtual-array"></a>Feladatátvétel virtuális tömbbe

Az eljárás futtatása előtt javasoljuk, hogy egy másik StorSimple virtuális tömböt hozzon létre, konfiguráljon és regisztráljon a StorSimple Eszközkezelő szolgáltatással.

> [!IMPORTANT]
> 
> * StorSimple 8000 sorozatú eszközről nem végezhető feladatátvétel egy 1200 virtuális eszközre.
> * Egy FIPS-kompatibilis virtuális eszközről egy másik FIPS-kompatibilis eszközre vagy egy, a kormányzati portálon üzembe helyezett nem FIPS-eszközre feladatátvételt végezhet.


A következő lépések végrehajtásával állíthatja vissza az eszközt egy célként megadott StorSimple virtuális eszközre.

1. Olyan céleszköz kiépítése és konfigurálása, amely megfelel az [eszköz feladatátvételének előfeltételeinek](#prerequisites). Fejezze be az eszköz konfigurációját a helyi webes felületen keresztül, és regisztrálja a StorSimple Eszközkezelő szolgáltatásban. Fájlkiszolgáló létrehozásakor lépjen a következő lépésre: [Set as File Server (kiszolgáló beállítása](storsimple-virtual-array-deploy3-fs-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device)). Ha iSCSI-kiszolgálót hoz létre, ugorjon az [iSCSI-kiszolgálóként való beállítás](storsimple-virtual-array-deploy3-iscsi-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device)1. lépésére.

2. Kötetek/megosztások offline állapotba helyezése a gazdagépen. A kötetek/megosztások offline állapotba helyezéséhez tekintse meg az operációs rendszer – a gazdagépre vonatkozó utasításokat. Ha még nem offline állapotban van, a következő lépésekkel offline állapotba kell állítania az összes kötetet/megosztást az eszközön.
   
    1. Lépjen az **eszközök** panelre, és válassza ki az eszközt.
   
    2. Válassza a **beállítások > > megosztások kezelése** (vagy a **beállítások > a > kötetek kezelése) lehetőséget**. 
   
    3. Válasszon ki egy megosztást/kötetet, kattintson a jobb gombbal, majd válassza az **Offline** állapotba állítás lehetőséget. 
   
    4. Ha a rendszer megerősítést kér, tekintse **meg a megosztás offline állapotba helyezésének következményeit.** 
   
    5. Kattintson az **offline állapotba** állítás elemre.

3. A StorSimple Eszközkezelő szolgáltatásban nyissa meg a **felügyeleti > eszközöket**. Az **eszközök** panelen válassza ki a forrás eszközét, és kattintson rá.

4. Az **eszköz irányítópult** paneljén kattintson az **inaktiválás** elemre.

5. Az **inaktiválás** panelen a rendszer megerősítést kér. Az eszköz inaktiválása olyan *állandó* folyamat, amely nem vonható vissza. Azt is emlékezteti, hogy a megosztásokat/köteteket offline állapotba helyezi a gazdagépen. A megerősítéshez írja be az eszköz nevét, majd kattintson az **inaktiválás** elemre.
   
    ![Képernyőkép az Inaktiválás panelről. Az eszköz neve mező ki van töltve, és az Inaktiválás gomb ki van emelve.](./media/storsimple-virtual-array-failover-dr/failover1.png)
6. Az Inaktiválás elindul. Az Inaktiválás sikeres befejeződése után értesítést fog kapni.
   
    ![Képernyőkép a folyamatjelzőről, amely azt jelzi, hogy az eszköz inaktiválva van.](./media/storsimple-virtual-array-failover-dr/failover2.png)
7. Az eszközök lapon az eszköz állapota mostantól **inaktiváltra** vált.
    ![Képernyőfelvétel az eszközök lapról. A deaktivált eszköz tulajdonságai megjelennek, beleértve az inaktivált állapotú állapotot is.](./media/storsimple-virtual-array-failover-dr/failover3.png)
8. Az **eszközök** panelen válassza ki a feladatátvételhez a deaktivált forrásoldali eszközt, és kattintson rá. 
9. Az **eszköz irányítópult** paneljén kattintson a **feladatátvétel** elemre. 
10. A **feladatátvételi eszköz** panelen tegye a következőket:
    
    1. A forrásoldali eszköz mező automatikusan ki van töltve. Jegyezze fel a forrásoldali eszköz teljes adatméretét. Az adatméretnek kisebbnek kell lennie, mint a megcélzott eszköz rendelkezésre álló kapacitása. Tekintse át a forrás eszközhöz társított adatokat, például az eszköz nevét, a teljes kapacitást és a feladatátvételi megosztások nevét.

    2. Az elérhető eszközök legördülő listából válassza ki a **kívánt eszközt**. A legördülő listában csak a megfelelő kapacitású eszközök jelennek meg.

    3. Győződjön meg arról, hogy **megértettem, hogy ez a művelet feladatátvételt hajt végre a célként megadott eszközön**. 

    4. Kattintson a **feladatátvétel** elemre.
    
        ![Képernyőfelvétel: a feladatátvételi eszköz panelje, a forrás és a cél eszköz kitöltésével, a beállítás be van jelölve, az átadott feladatátvétel gomb kiemelve.](./media/storsimple-virtual-array-failover-dr/failover4.png)
11. A feladatátvételi feladatok elindítják és értesítést kapnak. A feladatátvétel figyeléséhez nyissa meg az **eszközök > feladatok** lehetőséget.
    
     ![Képernyőkép – a folyamatjelző sáv, amely azt jelzi, hogy az eszköz feladatátvételt jelez.](./media/storsimple-virtual-array-failover-dr/failover5.png)
12. A **feladatok** panelen a forrás eszközhöz létrehozott feladatátvételi feladat jelenik meg. Ez a feladat hajtja végre a DR elővizsgálatokat.
    
    ![A feladatátvételi feladatok sikeres elindítását bemutató képernyőkép.](./media/storsimple-virtual-array-failover-dr/failover6.png)
    
     A DR-ellenőrzés sikeres befejezését követően a feladatátvételi feladat a forrásoldali eszközön található minden egyes megosztás/kötet esetében elindítja a visszaállítási feladatokat.
    
    ![A feladatátvételi feladattal, például az állapottal, az eszközzel és az időtartammal kapcsolatos részleteket bemutató képernyőkép.](./media/storsimple-virtual-array-failover-dr/failover7.png)
13. A feladatátvétel befejezése után lépjen az **eszközök** panelre.
    
    1. Jelölje ki, majd kattintson arra a StorSimple-eszközre, amelyet célként használt eszközként a feladatátvételi folyamathoz.
    2. Lépjen a **beállítások > felügyeleti > megosztások** (vagy a **kötetek** , ha az iSCSI-kiszolgáló). A **megosztások** panelen megtekintheti az összes megosztást (kötetet) a régi eszközről.
        ![Képernyőkép az eszközök panelről. A célként megadott eszköz online állapotú.](./media/storsimple-virtual-array-failover-dr/failover9.png)
14. [Létre kell hoznia egy DNS-aliast](https://web.archive.org/web/20150307000707/http://support.microsoft.com:80/kb/168322) , hogy az összes csatlakozni próbáló alkalmazás átirányítva legyen az új eszközre.

## <a name="errors-during-dr"></a>Hibák a DR

**Felhőbeli kapcsolódási leállás DR alatt**

Ha a DR megkezdése és az eszköz visszaállítása előtt megszakad a Felhőbeli kapcsolat, a DR sikertelen lesz. A rendszer hibaüzenetet küld. A DR megcélzott eszköze *használhatatlanként* van megjelölve. A jövőbeli DRs esetében nem használható ugyanaz a céleszköz.

**Nincsenek kompatibilis célként megadott eszközök**

Ha az elérhető céleszköz nem rendelkezik elegendő hellyel, hibaüzenet jelenik meg, amely szerint nincsenek kompatibilis eszközök.

**Előzetes ellenőrzési hibák**

Ha az előzetes ellenőrzések egyike nem teljesül, akkor az előzetes ellenőrzési hibák láthatók.

## <a name="business-continuity-disaster-recovery-bcdr"></a>Üzletmenet-folytonossági katasztrófa-helyreállítás (BCDR)

Az üzletmenet folytonossága vész-helyreállítási (BCDR) forgatókönyv akkor fordul elő, ha a teljes Azure-adatközpont működése leáll. Ez hatással lehet a StorSimple Eszközkezelő szolgáltatásra és a kapcsolódó StorSimple-eszközökre.

Ha vannak olyan StorSimple-eszközök, amelyek közvetlenül a katasztrófa előtt lettek regisztrálva, akkor előfordulhat, hogy ezeket a StorSimple-eszközöket törölni kell. A katasztrófa után újból létrehozhatja és konfigurálhatja ezeket az eszközöket.

## <a name="next-steps"></a>Következő lépések

Tudjon meg többet arról, hogyan [felügyelheti a StorSimple virtuális tömböt a helyi webes felhasználói felület használatával](storsimple-ova-web-ui-admin.md).