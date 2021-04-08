---
title: StorSimple virtuális tömb biztonsági mentésének klónozása | Microsoft Docs
description: Ismerje meg, hogyan készíthet biztonsági mentést, és hogyan állíthatja helyre a fájlokat a StorSimple virtuális tömbből.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: af6e979c-55e3-477c-b53e-a76a697f80c9
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: b6cc89cb082cd0ed32abd88e3a6683c60a27ba90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023040"
---
# <a name="clone-from-a-backup-of-your-storsimple-virtual-array"></a>Klónozás a StorSimple virtuális tömb biztonsági másolatából

## <a name="overview"></a>Áttekintés

Ez a cikk részletesen ismerteti a megosztások vagy kötetek biztonsági másolatának klónozását a Microsoft Azure StorSimple virtuális tömbben. A klónozott biztonsági másolat egy törölt vagy elveszett fájl helyreállítására szolgál. A cikk részletesen ismerteti, hogyan hajtható végre egy elemszintű helyreállítás a fájlkiszolgálón konfigurált StorSimple virtuális tömbben.

## <a name="clone-shares-from-a-backup-set"></a>Megosztások klónozása biztonságimásolat-készletből

**A megosztások klónozása előtt győződjön meg arról, hogy elegendő hely van az eszközön a művelet végrehajtásához.** A biztonsági másolatból történő klónozáshoz a [Azure Portal](https://portal.azure.com/)hajtsa végre az alábbi lépéseket.

#### <a name="to-clone-a-share"></a>Megosztás klónozása

1. Tallózással keresse meg az **eszközök** panelt. Jelölje ki, majd kattintson az eszközre, majd kattintson a **megosztások** lehetőségre. Válassza ki a klónozott megosztást, kattintson a jobb gombbal a megosztásra a helyi menü meghívásához. Válassza a **Clone** lehetőséget.
   
   ![Biztonsági másolat klónozása](./media/storsimple-virtual-array-clone/cloneshare1.png)
2. A **klónozás** panelen kattintson a **biztonsági mentés > válassza ki** , majd tegye a következőket: 
   
   a.    Az eszközön lévő biztonsági másolat szűrése az időtartomány alapján. Az **elmúlt 7 nap**, az **elmúlt 30 nap** és az **elmúlt év** közül választhat.
   
   b.    A szűrt biztonsági másolatok listájában válassza ki a klónozott biztonsági mentést.
   
   c.    Kattintson az **OK** gombra.
   
   ![Biztonsági másolat klónozása 2](./media/storsimple-virtual-array-clone/cloneshare3.png)
3. A **klónozás** panelen kattintson a **cél beállítások** elemre, majd tegye a következőket:
   
   a.    Adja meg a megosztás nevét. A megosztás nevének 3-127 karaktert kell tartalmaznia.
   
   b.    Opcionálisan megadhatja a klónozott megosztás leírását.
   
   c.    A visszaállítani kívánt megosztás típusa nem módosítható. A többplatformos megosztások klónozott módon, egy helyileg rögzített megosztásként kerülnek klónozásra.
   
   d.    A kapacitás úgy van beállítva, hogy megegyezzen a klónozott megosztás méretétől.
   
   e.    Rendelje hozzá a rendszergazdákat ehhez a megosztáshoz. A klónozott tulajdonságok a fájlkezelő befejezése után is módosíthatók.
   
   f.    Kattintson az **OK** gombra.
   
   ![Biztonsági másolat klónozása 3](./media/storsimple-virtual-array-clone/cloneshare6.png)

4. A klónozott feladatok elindításához kattintson a **klónozás** elemre. A feladatok befejezése után elindul a klónozási művelet, és értesítést kap. A klónozás állapotának figyeléséhez lépjen a **feladatok** panelre, és kattintson a feladatra a feladat részleteinek megtekintéséhez.
5. A klón sikeres létrehozása után váltson vissza az eszköz **megosztások** paneljére.
6. Most már megtekintheti az új klónozott megosztást az eszközön található megosztások listájában. A többplatformos megosztások klónozottként vannak beosztva, és helyileg rögzített megosztásként, helyileg rögzített megosztásként.
   
   ![Biztonsági másolat klónozása 4](./media/storsimple-virtual-array-clone/cloneshare10.png)

## <a name="clone-volumes-from-a-backup-set"></a>Kötetek klónozása biztonságimásolat-készletből

A biztonsági másolatból való klónozáshoz a Azure Portal a megosztás klónozásakor a-hoz hasonló lépéseket kell végrehajtania. A klónozási művelet a biztonsági mentést egy új kötetre klónozott egy virtuális eszközön; nem lehet klónozást elválasztani egy másik eszközre.

#### <a name="to-clone-a-volume"></a>Kötet klónozása

1. Tallózással keresse meg az **eszközök** panelt. Jelölje ki, majd kattintson az eszközre, majd kattintson a **kötetek** elemre. Válassza ki a klónozott kötetet, kattintson a jobb gombbal a kötetre a helyi menü meghívásához. Válassza a **Clone** lehetőséget.
   
   ![Kötet klónozása](./media/storsimple-virtual-array-clone/clonevolume1.png)
2. A **klónozás** panelen kattintson a **biztonsági mentés** elemre, majd tegye a következőket: 
   
   a.    Az eszközön lévő biztonsági másolat szűrése az időtartomány alapján. Az **elmúlt 7 nap**, az **elmúlt 30 nap** és az **elmúlt év** közül választhat. 
   
   b.    A szűrt biztonsági másolatok listájában válassza ki a klónozott biztonsági mentést.
   
   c.    Kattintson az **OK** gombra.
   
   ![2. kötet klónozása](./media/storsimple-virtual-array-clone/clonevolume3.png)
3. A **klónozás** panelen kattintson a **cél kötet beállításai** lehetőségre, majd tegye a következőket:
   
   a. Az eszköz neve automatikusan kitöltődik.
   
   b. Adja meg a **klónozott kötet** kötetének nevét. A kötet nevének 3 – 127 karaktert kell tartalmaznia.
   
   c. A kötet típusa automatikusan az eredeti kötetre lesz beállítva. A többszinten lévő köteteket a rendszer lépcsőzetesen, a helyileg rögzített kötetként pedig egy helyileg rögzített kötetként klónozott.
   
   d. A **csatlakoztatott gazdagépek** esetében kattintson a **kiválasztás** elemre.
   
   ![3. kötet klónozása](./media/storsimple-virtual-array-clone/clonevolume4.png)
4. A **csatlakoztatott gazdagépek** panelen válasszon egy meglévő ACR közül, vagy adjon hozzá egy új ACR-t. Új ACR hozzáadásához meg kell adnia egy ACR-nevet és a gazdagép IQN. Kattintson a **Kiválasztás** elemre.
   
   ![4. kötet klónozása](./media/storsimple-virtual-array-clone/clonevolume5.png)
5. A klónozott feladatok elindításához kattintson a **klónozás** elemre.
   
   ![5. kötet klónozása](./media/storsimple-virtual-array-clone/clonevolume6.png)  
6. A klónozási feladatok létrehozása után elindul a klónozás. A klón létrehozása után megjelenik az eszköz kötetek paneljén. Vegye figyelembe, hogy a többkötetes kötetek klónozása lépcsőzetesen történik, a helyileg rögzített kötetek pedig helyileg rögzített kötetként vannak klónozottak.
   
   ![6. kötet klónozása](./media/storsimple-virtual-array-clone/clonevolume8.png)
7. Ha a kötet megjelenik a kötetek listáján, a kötet elérhetővé válik. Az iSCSI-kezdeményező gazdagépen frissítse a célok listáját az iSCSI-kezdeményező tulajdonságai ablakban. A klónozott kötet nevét tartalmazó új cél "inaktívként" jelenik meg az Állapot oszlopban.
8. Válassza ki a célt, és kattintson a **kapcsolat** elemre. Miután a kezdeményező csatlakozott a célhoz, az állapotnak **csatlakoztatva** értékre kell váltania.
9. A **Lemezkezelés** ablakban a csatlakoztatott kötetek az alábbi ábrán látható módon jelennek meg. Kattintson a jobb gombbal a felderített kötetre (kattintson a lemez nevére), majd kattintson az **Online** elemre.

> [!IMPORTANT]
> Ha egy kötetet vagy egy megosztást egy biztonságimásolat-készletből próbál meg klónozott, ha a klónozási művelet meghiúsul, akkor a rendszer a portálon is létrehozhat egy célként megadott kötetet vagy megosztást. Fontos, hogy a célként megadott kötetet vagy megosztást a portálon törölje, hogy az ebből az elemből fakadó jövőbeli problémákat csökkentse.
> 
> 

## <a name="item-level-recovery-ilr"></a>Elemszintű helyreállítás (ILR)

Ez a kiadás bemutatja az elemszintű helyreállítást (ILR) egy fájlkiszolgálóként konfigurált StorSimple virtuális tömbben. A funkció lehetővé teszi a fájlok és mappák részletes helyreállítását a StorSimple-eszközön lévő összes megosztás Felhőbeli biztonsági másolatából. A legutóbbi biztonsági mentésből törölt fájlokat kérhet le önkiszolgáló modell használatával.

Minden megosztáshoz tartozik egy *. Backups* mappa, amely a legújabb biztonsági másolatokat tartalmazza. Navigáljon a kívánt biztonsági másolathoz, másolja a megfelelő fájlokat és mappákat a biztonsági másolatból, és állítsa vissza őket. Ez a szolgáltatás megszünteti a rendszergazdáknak a fájlok biztonsági másolatokból való visszaállítására irányuló hívásokat.

1. A ILR elvégzése során a Fájlkezelőben megtekintheti a biztonsági mentéseket. Kattintson arra a megosztásra, amelyre vonatkozóan meg szeretné jeleníteni a biztonsági mentést. Megjelenik az összes biztonsági mentést tároló megosztás alatt létrehozott *. Backups mappa.* Bontsa ki a *. Backups* mappát a biztonsági másolatok megtekintéséhez. A mappa a teljes biztonsági mentési hierarchia felrobbant nézetét jeleníti meg. Ez a nézet igény szerint jön létre, és általában csak néhány másodpercet vesz igénybe.
   
   Az utolsó öt biztonsági mentés így jelenik meg, és az elemszintű helyreállítás végrehajtásához használható. Az öt legutóbbi biztonsági mentés tartalmazza az alapértelmezett ütemezett és a manuális biztonsági mentést is.
   
   *  Az &lt; &gt; tulajdonsággal-ÉÉÉÉHHNN-HHMMSS-UTC néven nevű ütemezett biztonsági mentések.
   * Az ad-hoc-ÉÉÉÉHHNN-HHMMSS-UTC nevű **manuális biztonsági másolatok** .
     
     ![A. Backups mappát megjelenítő fájlkezelő képernyőképe. A mappában a manuális biztonsági másolatok mappa van kiválasztva.](./media/storsimple-virtual-array-clone/image14.png)

2. Azonosítsa a törölt fájl legújabb verzióját tartalmazó biztonsági mentést. Bár a mappa neve minden korábbi esetben UTC-időbélyeget tartalmaz, a mappa létrehozásának időpontja a tényleges eszköz, amikor a biztonsági mentés elindult. A biztonsági mentések megkereséséhez és azonosításához használja a mappa időbélyegét.

3. Keresse meg azt a mappát vagy fájlt, amelyet vissza szeretne állítani az előző lépésben azonosított biztonsági másolatban. Megjegyzés: csak azokat a fájlokat vagy mappákat tekintheti meg, amelyekhez jogosultsággal rendelkezik. Ha nem fér hozzá bizonyos fájlokhoz vagy mappákhoz, forduljon a megosztási rendszergazdához. A rendszergazda a fájlkezelő segítségével szerkesztheti a megosztási engedélyeket, és hozzáférést biztosíthat az adott fájlhoz vagy mappához. Ajánlott eljárás, hogy a megosztás rendszergazdája egyetlen felhasználó helyett felhasználói csoport legyen.

4. Másolja a fájlt vagy a mappát a megfelelő megosztásba a StorSimple fájlkiszolgálón.

## <a name="next-steps"></a>Következő lépések

Tudjon meg többet arról, hogyan [felügyelheti a StorSimple virtuális tömböt a helyi webes felhasználói felület használatával](storsimple-ova-web-ui-admin.md).

