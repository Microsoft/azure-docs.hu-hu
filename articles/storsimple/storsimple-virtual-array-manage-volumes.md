---
title: Kötetek kezelése a StorSimple virtuális tömbben | Microsoft Docs
description: Ismerteti a StorSimple Eszközkezelő, és ismerteti, hogyan használható a kötetek kezelése a StorSimple virtuális tömbben.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: caa6a26b-b7ba-4a05-b092-1a79450225cf
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 79e3ce8c1605e5d68ff44901f53854d2f5f10abc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96014380"
---
# <a name="use-storsimple-device-manager-service-to-manage-volumes-on-the-storsimple-virtual-array"></a>Kötetek kezelése a StorSimple-eszközkezelő szolgáltatással a StorSimple Virtual Arrayben

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag azt ismerteti, hogy a StorSimple Eszközkezelő szolgáltatás használatával hogyan hozhat létre és kezelhet köteteket a StorSimple virtuális tömbben.

A StorSimple Eszközkezelő szolgáltatás a Azure Portal egyik bővítménye, amely lehetővé teszi a StorSimple-megoldás egyetlen webes felületen való kezelését. A megosztások és kötetek kezelése mellett a StorSimple Eszközkezelő szolgáltatás segítségével megtekintheti és kezelheti az eszközöket, megtekintheti a riasztásokat, valamint megtekintheti és kezelheti a biztonsági mentési házirendeket és a biztonsági mentési katalógust.

## <a name="volume-types"></a>Kötetek típusai

A StorSimple kötetek a következőket tehetik:

* **Helyileg rögzített**: az ezekben a kötetekben tárolt adatmennyiség mindig a tömbön marad, és nem kerül a felhőbe.
* **Lépcsőzetes**: ezekben a kötetekben lévő adatforgalom a felhőbe kerül. A rétegű kötetek létrehozásakor a terület körülbelül 10%-a a helyi szinten van kiépítve, a terület 90%-a pedig a felhőben van kiépítve. Ha például egy 1 TB-os kötetet telepített, a 100 GB a helyi térben, a 900 GB-ot pedig a felhőben fogja használni az adatrétegeknél. Ez pedig azt jelenti, hogy ha elfogyott az eszközön lévő összes helyi terület, nem lehet kiépíteni egy rétegű kötetet (mivel a helyi szinten szükséges 10% nem lesz elérhető).

### <a name="provisioned-capacity"></a>Kiosztott kapacitás
Tekintse meg az alábbi táblázatot az egyes kötetek kiosztott kapacitásának maximális kiosztásáról.

| **Korlátazonosító**                                       | **Korlátot**     |
|------------------------------------------------------------|---------------|
| Egy többszintű kötet minimális mérete                            | 500 GB        |
| Egy rétegű kötet maximális mérete                            | 5 TB          |
| Egy helyileg rögzített kötet minimális mérete                    | 50 GB         |
| Egy helyileg rögzített kötet maximális mérete                    | 200 GB        |

## <a name="the-volumes-blade"></a>A kötetek panel
A StorSimple szolgáltatás összegzése panel **kötetek** menüje megjeleníti egy adott StorSimple-tömbben lévő tárolási kötetek listáját, és lehetővé teszi a felügyeletét.

![Kötetek panel](./media/storsimple-virtual-array-manage-volumes/volumes-blade.png)

A kötetek számos attribútumból állnak:

* **Kötet neve** – egy leíró név, amelynek egyedinek kell lennie, és segít azonosítani a kötetet.
* Status – online vagy offline **állapotú** lehet. Ha egy kötet offline állapotban van, nem látható azoknak a kezdeményezőknek (kiszolgálóknak), amelyek számára engedélyezett a kötet használata.
* **Type (típus** ) – azt jelzi, hogy a kötet **Lépcsőzetes** (alapértelmezett) vagy **helyileg rögzített**-e.
* **Kapacitás** – a kezdeményező (kiszolgáló) által tárolható összes adatmennyiséghez képest felhasznált adatmennyiséget határozza meg.
* **Backup (biztonsági mentés** ) – a StorSimple virtuális tömb esetében az összes kötet automatikusan engedélyezve lesz a biztonsági mentéshez.
* **Csatlakoztatott gazdagépek** – megadja azokat a kezdeményezőket (kiszolgálókat), amelyek számára engedélyezett a kötethez való hozzáférés.

![Kötetek részletei](./media/storsimple-virtual-array-manage-volumes/volume-details.png)

Az oktatóanyag utasításait az alábbi feladatok végrehajtásához használhatja:

* Kötet hozzáadása
* Kötet módosítása
* Kötet offline állapotba helyezése
* Kötet törlése

## <a name="add-a-volume"></a>Kötet hozzáadása

1. A StorSimple szolgáltatás összegzése panelen kattintson a **+ kötet hozzáadása** parancsra a parancssorban. Ekkor megnyílik a **kötet hozzáadása** panel.
   
    ![Képernyőfelvétel: a kötet hozzáadása gomb és a kötet hozzáadása panel.](./media/storsimple-virtual-array-manage-volumes/add-volume.png)
2. A **kötet hozzáadása** panelen tegye a következőket:
   
   * A **kötet neve** mezőben adjon meg egy egyedi nevet a kötetnek. A névnek olyan sztringnek kell lennie, amely 3 – 127 karaktert tartalmaz.
   * A **típus** legördülő listában adja meg, hogy létre kell-e hozni egy **rétegű** vagy **helyileg rögzített** kötetet. Helyi garanciákat, kis késést és nagyobb teljesítményt igénylő munkaterhelések esetén válassza a **helyileg rögzített kötet** lehetőséget. Az összes többi adattal válassza a **Lépcsőzetes** kötet lehetőséget.
   * A **kapacitás** mezőben határozza meg a kötet méretét. A rétegű kötetnek 500 GB és 5 TB közé kell esnie, és egy helyileg rögzített kötetnek 50 GB és 500 GB között kell lennie.
   * * Kattintson a **csatlakoztatott gazdagépek** elemre, jelölje ki a kötethez csatlakozni kívánt iSCSI-kezdeményezőhöz tartozó hozzáférés-vezérlési REKORDOT (ACR), majd kattintson a **kiválasztás** gombra.
3. Új csatlakoztatott gazdagép hozzáadásához kattintson az **új hozzáadása** lehetőségre, adja meg a gazdagép nevét és az iSCSI minősített nevét (IQN), majd kattintson a **Hozzáadás** gombra.
   
    ![A képernyőfelvétel megjeleníti a csatlakoztatott gazdagépek panelt, ahol új hozzáadást adhat hozzá.](./media/storsimple-virtual-array-manage-volumes/volume-add-acr.png)
4. Ha befejezte a kötet konfigurálását, kattintson a **Létrehozás** gombra. A rendszer létrehoz egy kötetet a megadott beállításokkal, és egy értesítést fog látni a sikeres létrehozásáról. Alapértelmezés szerint a biztonsági mentés engedélyezve lesz a köteten.
5. A kötet sikeres létrehozásának ellenőrzéséhez nyissa meg a **kötetek** panelt. Ekkor meg kell jelennie a felsorolt kötetnek.
   
    ![A kötet létrehozása sikeres](./media/storsimple-virtual-array-manage-volumes/volume-success.png)

## <a name="modify-a-volume"></a>Kötet módosítása

Módosítsa a kötetet, ha módosítania kell a kötetet elérő gazdagépeket. A kötet többi attribútuma nem módosítható a kötet létrehozása után.

#### <a name="to-modify-a-volume"></a>Kötet módosítása

1. A StorSimple szolgáltatás összegzése panel **kötetek** beállításainál válassza ki azt a virtuális tömböt, amelyen a módosítani kívánt kötet található.
2. **Válassza ki** a kötetet, és kattintson a **csatlakoztatott gazdagépek** elemre a jelenleg csatlakoztatott gazdagép megtekintéséhez, majd módosítsa egy másik kiszolgálóra.
   
    ![Kötet szerkesztése](./media/storsimple-virtual-array-manage-volumes/volume-edit-acr.png)
3. Mentse a módosításokat a **Mentés** parancssáv gombra kattintva. A rendszer alkalmazza a megadott beállításokat, és értesítést fog látni.

## <a name="take-a-volume-offline"></a>Kötet offline állapotba helyezése

Előfordulhat, hogy egy kötetet offline állapotba kell állítania, ha azt tervezi, hogy módosítja vagy törli. Ha egy kötet offline állapotban van, nem érhető el írási és olvasási hozzáféréssel. A kötetet offline állapotba kell helyeznie a gazdagépen, valamint az eszközön is.

#### <a name="to-take-a-volume-offline"></a>Kötet offline állapotba helyezése

1. Az offline állapotba helyezés előtt győződjön meg arról, hogy a szóban forgó kötet nincs használatban.
2. Először állítsa offline állapotba a kötetet a gazdagépen. Ezzel kiküszöbölheti az adatsérülés lehetséges kockázatát a köteten. Adott lépésekért tekintse meg a gazdagép operációs rendszerének utasításait.
3. Miután a gazdagépen lévő kötet offline állapotban van, a következő lépések végrehajtásával állítsa offline állapotba a tömböt a tömbön:
   
   * A StorSimple szolgáltatás összegzése panel **kötetek** beállításainál válassza ki azt a virtuális tömböt, amelyen az offline állapotú kötetet szeretné tárolni.
   * **Válassza ki** a kötetet, majd kattintson a **...** elemre (a jobb gombbal kattintson erre a sorra), majd a helyi menüben válassza az **Offline** állapotba állítás lehetőséget.
     
        ![Offline kötet](./media/storsimple-virtual-array-manage-volumes/volume-offline.png)
   * Tekintse át az adatokat az **Offline** panelen, és erősítse meg a művelet elfogadását. A kötet offline állapotba helyezéséhez kattintson az **Offline** állapotba állítás lehetőségre. Ekkor megjelenik egy értesítés a folyamatban lévő műveletről.
   * A **kötetek** panel megnyitásával ellenőrizheti, hogy a kötet offline állapotba került-e. A kötet állapota kapcsolat nélküli üzemmódban jelenik meg.
     
       ![Kapcsolat nélküli kötet megerősítése](./media/storsimple-virtual-array-manage-volumes/volume-offline-confirm.png)

## <a name="delete-a-volume"></a>Kötet törlése

> [!IMPORTANT]
> Kötetet csak akkor törölhet, ha offline állapotban van.
> 
> 

A kötetek törléséhez hajtsa végre az alábbi lépéseket.

#### <a name="to-delete-a-volume"></a>Kötet törlése

1. A StorSimple szolgáltatás összegzése panel **kötetek** beállításainál válassza ki azt a virtuális tömböt, amelyen a törölni kívánt kötet található.
2. **Válassza ki** a kötetet, majd kattintson a **...** elemre (a jobb gombbal kattintson erre a sorra), majd a helyi menüben válassza a **Törlés** lehetőséget.
   
    ![Kötet törlése](./media/storsimple-virtual-array-manage-volumes/volume-delete.png)
3. Tekintse meg a törölni kívánt kötet állapotát. Ha a törölni kívánt kötet nem offline állapotban van, először kapcsolja offline állapotba, majd kövesse a [kötet offline](#take-a-volume-offline)állapotba helyezése című témakör lépéseit.
4. Amikor a rendszer megerősítést kér a **Törlés** panelen, fogadja el a megerősítést, és kattintson a **Törlés** gombra. A kötet most már törölve lesz, és a **kötetek** panel megjeleníti a kötetek frissített listáját a virtuális tömbön belül.

## <a name="next-steps"></a>Következő lépések

Tudnivalók a [StorSimple-kötetek klónozásáról](storsimple-virtual-array-clone.md).

