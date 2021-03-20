---
title: StorSimple virtuális tömb megosztásának kezelése | Microsoft Docs
description: Ismerteti a StorSimple Eszközkezelő, és ismerteti, hogyan használható a megosztások kezelése a StorSimple virtuális tömbben.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: 0a799c83-fde5-4f3f-af0e-67535d1882b6
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 340b910319cabe3379bdb1bad1c09bc71c17f072
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994944"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-shares-on-the-storsimple-virtual-array"></a>Megosztások kezelése a StorSimple-eszközkezelő szolgáltatással a StorSimple Virtual Arrayben

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag azt ismerteti, hogyan használható a StorSimple Eszközkezelő szolgáltatás a megosztások létrehozásához és kezeléséhez a StorSimple virtuális tömbben.

A StorSimple Eszközkezelő szolgáltatás a Azure Portal egyik bővítménye, amely lehetővé teszi a StorSimple-megoldás egyetlen webes felületen való kezelését. A megosztások és kötetek kezelése mellett a StorSimple Eszközkezelő szolgáltatás segítségével megtekintheti és kezelheti az eszközöket, megtekintheti a riasztásokat, kezelheti a biztonsági mentési házirendeket, és kezelheti a biztonsági mentési katalógust.

## <a name="share-types"></a>Megosztási típusok

A StorSimple-megosztások a következőket tehetik:

* **Helyileg rögzített**: az ezekben a megosztásokban tárolt adatmennyiség mindig a tömbön marad, és nem kerül a felhőbe.
* **Lépcsőzetes**: ezekben a megosztásokban lévő adatforgalom a felhőbe kerül. Többplatformos megosztás létrehozásakor a terület körülbelül 10%-a a helyi szinten van kiépítve, a terület 90%-a pedig a felhőben van kiépítve. Ha például 1 TB-os megosztást telepített, a 100 GB a helyi térben, a 900 GB pedig a felhőben lesz használatban az adatszintek esetében. Ez pedig azt jelenti, hogy ha elfogyott az eszközön lévő összes helyi terület, nem lehet kiépíteni egy többplatformos megosztást (mivel a helyi szinten szükséges 10% nem lesz elérhető).

### <a name="provisioned-capacity"></a>Kiosztott kapacitás

Tekintse meg az alábbi táblázatot az egyes megosztások maximális kiosztott kapacitásához.

| **Korlátazonosító** | **Korlátot** |
| --- | --- |
| Többszintű megosztás minimális mérete |500 GB |
| Lépcsőzetes megosztás maximális mérete |20 TB |
| Egy helyileg rögzített megosztás minimális mérete |50 GB |
| Egy helyileg rögzített megosztás maximális mérete |2 TB |

## <a name="the-shares-blade"></a>A megosztások panel

A StorSimple szolgáltatás összefoglaló paneljének **megosztások** menüjében megjelenik egy adott StorSimple-tömbben található tárolási megosztások listája, és így kezelheti azokat.

![Megosztások panel](./media/storsimple-virtual-array-manage-shares/shares-blade.png)

A megosztás számos attribútumból áll:

* **Megosztás neve** – egy leíró név, amelynek egyedinek kell lennie, és segít azonosítani a megosztást.
* Status – online vagy offline **állapotú** lehet. Ha egy megosztás offline állapotban van, a megosztás felhasználói nem fognak tudni hozzáférni.
* **Type (típus** ) – azt jelzi, hogy a megosztás **Lépcsőzetes** (alapértelmezett) vagy **helyileg rögzített**-e.
* **Kapacitás** – meghatározza a megosztáson tárolható adatmennyiséghez képest felhasznált adatmennyiséget.
* **Description (Leírás** ) – választható beállítás, amely segít a megosztás leírásában.
* **Engedélyek** – a Windows Intéző használatával felügyelhető megosztás NTFS-engedélyei.
* **Backup (biztonsági mentés** ) – a StorSimple virtuális tömb esetében az összes megosztás automatikusan engedélyezve lesz a biztonsági mentéshez.

![Megosztások részletei](./media/storsimple-virtual-array-manage-shares/share-details.png)

Az oktatóanyag utasításait az alábbi feladatok végrehajtásához használhatja:

* Megosztás hozzáadása
* Megosztás módosítása
* Megosztás offline állapotba helyezése
* Megosztás törlése

## <a name="add-a-share"></a>Megosztás hozzáadása

1. A StorSimple szolgáltatás összegzése panelen kattintson a **+ megosztás hozzáadása** parancsra a parancssorban. Ekkor megnyílik a **megosztás hozzáadása** panel.

    ![Megosztás hozzáadása](./media/storsimple-virtual-array-manage-shares/add-share.png)

2. A **megosztás hozzáadása** panelen tegye a következőket:
   
   1. A **megosztás neve** mezőbe írja be a megosztás egyedi nevét. A névnek olyan sztringnek kell lennie, amely 3 – 127 karaktert tartalmaz.

   2. A megosztás opcionális **leírása** . A Leírás segítséget nyújt a megosztási tulajdonosok azonosításában.

   3. A **típus** legördülő listában adja meg, hogy létre kell-e hozni egy **Lépcsőzetes** vagy **helyileg rögzített** megosztást. Helyi garanciákat, kis késést és nagyobb teljesítményt igénylő munkaterhelések esetén válassza a **helyileg rögzített megosztás** lehetőséget. Az összes többi adattal válassza a **Lépcsőzetes** megosztás lehetőséget.

   4. A **kapacitás** mezőben adja meg a megosztás méretét. A többplatformos megosztásnak 500 GB és 20 TB közé kell esnie, és egy helyileg rögzített megosztásnak 50 GB és 2 TB között kell lennie.

   5. Az **alapértelmezett teljes engedélyek beállítása** mezőben rendelje hozzá az engedélyeket a felhasználóhoz, vagy a megosztáshoz hozzáférő csoportot. Adja meg a felhasználó vagy a felhasználói csoport nevét _john@contoso.com_ formátumban. Azt javasoljuk, hogy egy felhasználói csoportot használjon (egyetlen felhasználó helyett), hogy a rendszergazdai jogosultságok hozzáférjenek a megosztásokhoz. A hozzárendelt engedélyeket később a Fájlkezelővel módosíthatja.
3. Ha befejezte a megosztás konfigurálását, kattintson a **Létrehozás** gombra. A rendszer létrehoz egy megosztást a megadott beállításokkal, és értesítést fog látni. Alapértelmezés szerint a biztonsági mentés engedélyezve lesz a megosztáshoz.
4. A megosztás sikeres létrejöttének ellenőrzéséhez nyissa meg a **megosztások** panelt. Ekkor meg kell jelennie a felsorolt megosztásnak.
   
    ![Megosztás sikeres létrehozása](./media/storsimple-virtual-array-manage-shares/share-success.png)

## <a name="modify-a-share"></a>Megosztás módosítása

Ha módosítani szeretné a megosztás leírását, módosítsa a megosztást. A megosztás létrehozása után más megosztási tulajdonságok nem módosíthatók.

#### <a name="to-modify-a-share"></a>Megosztás módosítása

1. A StorSimple szolgáltatás összegzése panel **megosztások** beállítása paneljén válassza ki azt a virtuális tömböt, amelyen a módosítani kívánt megosztás található.
2. **Válassza ki** a megosztást az aktuális Leírás megtekintéséhez és a módosításhoz.
3. Mentse a módosításokat a **Mentés** parancssáv gombra kattintva. A rendszer alkalmazza a megadott beállításokat, és értesítést fog látni.
   
    ![ Megosztás szerkesztése](./media/storsimple-virtual-array-manage-shares/share-edit.png)

## <a name="take-a-share-offline"></a>Megosztás offline állapotba helyezése

Előfordulhat, hogy egy megosztást offline állapotba kell állítania, ha módosítani vagy törölni szeretné. Ha egy megosztás offline állapotban van, nem érhető el írási és olvasási hozzáféréssel. A megosztást offline állapotba kell helyeznie a gazdagépen és az eszközön is.

#### <a name="to-take-a-share-offline"></a>Megosztás offline állapotba helyezése

1. Az offline állapotba helyezés előtt győződjön meg arról, hogy a szóban forgó megosztás nincs használatban.
2. A következő lépések végrehajtásával végezze el a megosztást a tömbön offline állapotba:
   
    1. A StorSimple szolgáltatás összegzése panel **megosztások** beállítása paneljén válassza ki azt a virtuális tömböt, amelyen az offline állapotú megosztást szeretné.

    2. **Válassza ki** a megosztást, és kattintson a **...** elemre (a jobb gombbal kattintson erre a sorra), majd a helyi menüben válassza az **Offline** állapotba állítás lehetőséget.
     
        ![Offline megosztás](./media/storsimple-virtual-array-manage-shares/shares-offline.png)

    3. Tekintse át az adatokat az **Offline** panelen, és erősítse meg a művelet elfogadását. Kattintson az **Offline** állapotba helyezés gombra a megosztás offline állapotba helyezéséhez. Ekkor megjelenik egy értesítés a folyamatban lévő műveletről.

    4. A megosztás offline állapotba helyezésének ellenőrzéséhez nyissa meg a **megosztások** panelt. A megosztás állapotát kapcsolat nélküli állapotban kell látni.

## <a name="delete-a-share"></a>Megosztás törlése

> [!IMPORTANT]
> A megosztás csak akkor törölhető, ha offline állapotban van.


Egy megosztás törléséhez hajtsa végre az alábbi lépéseket.

#### <a name="to-delete-a-share"></a>Megosztás törlése

1. A StorSimple szolgáltatás összegzése panel **megosztások** beállítása paneljén válassza ki azt a virtuális tömböt, amelyen a törölni kívánt megosztás található.
2. **Válassza ki** a megosztást, és kattintson a **...** elemre (a jobb gombbal kattintson erre a sorra), majd a helyi menüben válassza a **Törlés** lehetőséget.
   
    ![Megosztás törlése](./media/storsimple-virtual-array-manage-shares/share-delete.png)
3. Tekintse meg a törölni kívánt megosztás állapotát. Ha a törölni kívánt megosztás nem offline állapotban van, először kapcsolja offline állapotba. Kövesse a [megosztás offline állapotba](#take-a-share-offline)helyezése című témakör lépéseit.
4. Amikor a rendszer megerősítést kér a **Törlés** panelen, fogadja el a megerősítést, és kattintson a **Törlés** gombra. A megosztás törölve lesz, és a **megosztások** panel a virtuális tömbben található megosztások frissített listáját jeleníti meg.

## <a name="next-steps"></a>Következő lépések
Útmutató StorSimple- [megosztás klónozásához](storsimple-virtual-array-clone.md).

