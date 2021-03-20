---
title: Virtuális tömb biztonsági mentésének Microsoft Azure StorSimple oktatóanyaga | Microsoft Docs
description: Ütemezett és manuális biztonsági mentéseket hozhat létre Microsoft Azure StorSimple virtuális tömbhöz, és elemszintű helyreállítást hajthat végre a törölt fájlok virtuális tömbön való visszaállításához.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: e3cdcd9e-33b1-424d-82aa-b369d934067e
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 99c7f4ab92e4cce3937a933fec97008c281a0eae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018290"
---
# <a name="back-up-shares-or-volumes-on-your-storsimple-virtual-array"></a>A StorSimple virtuális tömbben lévő megosztások vagy kötetek biztonsági mentése

## <a name="overview"></a>Áttekintés

A StorSimple Virtual Array egy olyan hibrid felhőalapú tároló, amely egy fájlkiszolgáló vagy egy iSCSI-kiszolgálóként konfigurálható helyszíni virtuális eszköz. A virtuális tömb lehetővé teszi, hogy a felhasználó ütemezett és manuális biztonsági másolatokat hozzon létre az eszközön található összes megosztásról vagy kötetről. Ha fájlkiszolgálóként van konfigurálva, akkor az elemszintű helyreállítást is lehetővé teszi. Ez az oktatóanyag leírja, hogyan hozhat létre ütemezett és manuális biztonsági mentést, és hogyan végezhet elemszintű helyreállítást a törölt fájlok virtuális tömbön való visszaállításához.

Ez az oktatóanyag csak a StorSimple virtuális tömbökre vonatkozik. Az 8000 sorozattal kapcsolatos információkért látogasson el a [biztonsági másolat létrehozása a 8000 Series eszközhöz](./storsimple-8000-manage-backup-policies-u2.md) című témakörben.

## <a name="back-up-shares-and-volumes"></a>Megosztások és kötetek biztonsági mentése

A biztonsági másolatok időponthoz kapcsolódó védelmet biztosítanak, javíthatják a helyreállítást, és csökkenthetik a megosztások és kötetek visszaállítási idejét. A StorSimple-eszközön lévő megosztások vagy kötetek biztonsági mentését kétféleképpen végezheti el: **ütemezett** vagy **manuális**. A módszerek mindegyikét a következő szakaszokban tárgyaljuk.

## <a name="change-the-backup-start-time"></a>A biztonsági mentés kezdési idejének módosítása

> [!NOTE]
> Ebben a kiadásban az ütemezett biztonsági mentéseket egy olyan alapértelmezett házirend hozza létre, amely egy adott időpontban naponta fut, és biztonsági másolatot készít az eszközön található összes megosztásról vagy kötetről. Jelenleg nem lehet egyéni szabályzatokat létrehozni az ütemezett biztonsági mentésekhez.


A StorSimple Virtual Array egy alapértelmezett biztonsági mentési szabályzattal rendelkezik, amely egy adott napszakban indul (22:30), és naponta egyszer biztonsági másolatot készít az eszközön található összes megosztásról vagy kötetről. Megváltoztathatja a biztonsági mentés elindulásának időpontját, de a gyakoriságot és az adatmegőrzést (amely meghatározza a megőrizni kívánt biztonsági másolatok számát) nem módosítható. A biztonsági mentések során a teljes virtuális eszköz biztonsági mentése történik. Ez befolyásolhatja az eszköz teljesítményét, és hatással lehet az eszközön üzembe helyezett munkaterhelésekre. Ezért javasoljuk, hogy a biztonsági mentéseket a következő időpontra ütemezze.

 Az alapértelmezett biztonsági mentés kezdési idejének módosításához hajtsa végre az alábbi lépéseket a [Azure Portal](https://portal.azure.com/).

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>Az alapértelmezett biztonsági mentési szabályzat kezdő időpontjának módosítása

1. Válassza az **eszközök** lehetőséget. Ekkor megjelenik a StorSimple Eszközkezelő szolgáltatásban regisztrált eszközök listája. 
   
    ![Navigáljon az eszközökhöz](./media/storsimple-virtual-array-backup/changebuschedule1.png)

2. Válassza ki, majd kattintson az eszközre. Ekkor megjelenik a **Settings (beállítások** ) panel. Lépjen a **> biztonsági mentési szabályzatok kezelése** című lépésre.
   
    ![eszköz kiválasztása](./media/storsimple-virtual-array-backup/changebuschedule2.png)

3. A **biztonsági mentési szabályzatok** panelen az alapértelmezett indítási idő a 22:30. Megadhatja a napi ütemterv új kezdési idejét az eszköz időzónájában.
   
    ![Navigáljon a biztonsági mentési szabályzatokhoz](./media/storsimple-virtual-array-backup/changebuschedule5.png)

4. Kattintson a **Mentés** gombra.

### <a name="take-a-manual-backup"></a>Manuális biztonsági mentés készítése

Az ütemezett biztonsági mentések mellett bármikor manuálisan (igény szerinti) biztonsági mentést készíthet az eszközökről.

#### <a name="to-create-a-manual-backup"></a>Manuális biztonsági mentés létrehozása

1. Válassza az **eszközök** lehetőséget. Válassza ki az eszközt, majd kattintson a jobb gombbal a kijelölt sor jobb szélén található **...** lehetőségre. A helyi menüben válassza a **biztonsági másolat** készítése lehetőséget.
   
    ![a biztonsági másolat készítéséhez navigáljon](./media/storsimple-virtual-array-backup/takebackup1m.png)

2. A **biztonsági mentés** készítése panelen kattintson a **biztonsági mentés** készítése elemre. Ez a szolgáltatás a fájlkiszolgálón lévő összes megosztást vagy az iSCSI-kiszolgálón lévő összes kötetet biztonsági másolatot készíti. 
   
    ![biztonsági mentés indítása](./media/storsimple-virtual-array-backup/takebackup2m.png)
   
    Az igény szerinti biztonsági mentés elindul, és látni fogja, hogy a biztonsági mentési feladatok megkezdődtek.
   
    ![a biztonsági mentés 2. kezdete](./media/storsimple-virtual-array-backup/takebackup3m.png) 
   
    Ha a feladatot sikeresen elvégezte, értesítést kap. Ekkor elindul a biztonsági mentési folyamat.
   
    ![biztonsági mentési feladatok létrehozva](./media/storsimple-virtual-array-backup/takebackup4m.png)

3. A biztonsági másolatok állapotának nyomon követéséhez és a feladatok részleteinek megtekintéséhez kattintson az értesítésre. Ezzel elvégzi a  **feladatok részleteit**.
   
     ![biztonsági mentési feladatok részletei](./media/storsimple-virtual-array-backup/takebackup5m.png)

4. A biztonsági mentés befejezése után nyissa meg a **felügyeleti > biztonsági mentési katalógust**. Ekkor megjelenik egy Felhőbeli pillanatkép az eszközön található összes megosztásról (vagy kötetről).
   
    ![Befejezett biztonsági mentés](./media/storsimple-virtual-array-backup/takebackup19m.png) 

## <a name="view-existing-backups"></a>Meglévő biztonsági másolatok megtekintése
A meglévő biztonsági másolatok megtekintéséhez hajtsa végre a következő lépéseket a Azure Portalban.

#### <a name="to-view-existing-backups"></a>Meglévő biztonsági másolatok megtekintése

1. Lépjen az **eszközök** panelre. Válassza ki, majd kattintson az eszközre. A **Beállítások** panelen lépjen a **felügyelet > biztonsági mentési katalógus** elemre.
   
    ![Navigáljon a biztonsági mentési katalógushoz](./media/storsimple-virtual-array-backup/viewbackups1.png)
2. A szűréshez használandó következő feltételek megadása:
   
   - **Időtartomány** – lehet az **elmúlt 1 óra**, az **elmúlt 24 óra**, az **elmúlt 7 nap**, az **elmúlt 30 nap**, az **elmúlt év** és az **Egyéni dátum**.
    
   - **Eszközök** – válassza ki a StorSimple Eszközkezelő szolgáltatásban regisztrált fájlkiszolgálók vagy iSCSI-kiszolgálók listáját.
   
   - **Kezdeményező** – automatikusan **ütemezhető** (biztonsági mentési házirend alapján), vagy **manuálisan** kezdeményezhető (Ön).
   
     ![Biztonsági másolatok szűrése](./media/storsimple-virtual-array-backup/viewbackups2.png)

3. Kattintson az **Alkalmaz** gombra. A biztonsági másolatok szűrt listája megjelenik a **biztonsági mentési katalógus** panelen. Megjegyzés: csak az 100 biztonsági mentési elemek jeleníthetők meg egy adott időpontban.
   
    ![Frissített biztonsági mentési katalógus](./media/storsimple-virtual-array-backup/viewbackups3.png)

## <a name="next-steps"></a>Következő lépések

További információ [a StorSimple virtuális tömb felügyeletéről](storsimple-ova-web-ui-admin.md).