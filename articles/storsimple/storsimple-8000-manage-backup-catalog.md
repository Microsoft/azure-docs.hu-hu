---
title: A StorSimple biztonsági mentési katalógusának kezelése | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használhatók a biztonságimásolat-készletek a StorSimple Eszközkezelő szolgáltatás biztonsági mentési katalógusának használatával.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 6ab4694159c6269f2f72d3d591147d43aef34a0a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017372"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-backup-catalog"></a>A StorSimple Eszközkezelő szolgáltatás használata a biztonsági mentési katalógus kezelésére
## <a name="overview"></a>Áttekintés
A StorSimple Eszközkezelő szolgáltatás **biztonságimásolat-katalógusa** panel megjeleníti a manuális vagy ütemezett biztonsági mentések során létrehozott összes biztonságimásolat-készletet. Ezen a lapon megtekintheti a biztonsági mentési szabályzatok vagy kötetek összes biztonsági mentését, kiválaszthatja vagy törölheti a biztonsági másolatokat, vagy biztonsági másolattal állíthatja vissza vagy klónozott kötetek klónozását.

Ez az oktatóanyag a biztonságimásolat-készlet listázását, kiválasztását és törlését ismerteti. Ha szeretné megtudni, hogyan állíthatja vissza az eszközt a biztonsági másolatból, nyissa meg az [eszköz visszaállítását egy biztonságimásolat-készletből](storsimple-8000-restore-from-backup-set-u2.md). A kötetek klónozásával kapcsolatban tekintse meg a [StorSimple-kötet klónozása](storsimple-8000-clone-volume-u2.md)című témakört.

![Biztonsági mentési katalógus](./media/storsimple-8000-manage-backup-catalog/bucatalog.png) 

A **biztonságimásolat-katalógus** panel egy lekérdezést biztosít a biztonságimásolat-készlet kijelölésének szűkítéséhez. A lekérdezett biztonságimásolat-készleteket a következő paraméterek alapján szűrheti:

* **Eszköz** – az eszköz, amelyen a biztonságimásolat-készlet létrejött.
* **Biztonsági mentési házirend vagy kötet** – a biztonságimásolat-készlethez társított biztonsági mentési szabályzat vagy kötet.
* **És** – – a biztonságimásolat-készlet létrehozásakor megjelenő dátum-és időtartomány.

A szűrt biztonságimásolat-készletek a következő attribútumok alapján lesznek táblázatos formában:

* **Name (név** ) – a biztonságimásolat-készlethez társított biztonsági mentési szabályzat vagy kötet neve.
* **Size (méret** ) – a biztonságimásolat-készlet tényleges mérete.
* **Létrehozva** – a biztonsági másolatok létrehozásának dátuma és időpontja. 
* **Típus** – a biztonságimásolat-készletek helyi Pillanatképek vagy Felhőbeli Pillanatképek lehetnek. A helyi pillanatkép az eszközön helyileg tárolt összes kötet-adatok biztonsági mentése, míg a Felhőbeli pillanatkép a felhőben található kötet-adatok biztonsági mentésére utal. A helyi Pillanatképek gyorsabb hozzáférést biztosítanak, míg a Felhőbeli Pillanatképek az adatrugalmasság érdekében vannak kiválasztva.
* **Kezdeményező** – a biztonsági mentéseket automatikusan ütemezheti vagy manuálisan is elindíthatja a felhasználó. Biztonsági mentési szabályzatot használhat a biztonsági mentések ütemezéséhez. Azt is megteheti, hogy a **biztonsági mentés** lehetőség használatával manuális biztonsági mentést készít.

## <a name="list-backup-sets-for-a-backup-policy"></a>Biztonsági mentési szabályzathoz tartozó biztonságimásolat-készletek listázása
A biztonsági mentési szabályzat összes biztonsági mentésének listázásához hajtsa végre az alábbi lépéseket.

#### <a name="to-list-backup-sets"></a>Biztonságimásolat-készletek listázása
1. Lépjen a StorSimple Eszközkezelő szolgáltatásra, és kattintson a **Backup Catalog** elemre.

2. A következő módon szűrheti a beállításokat:
   
   1. Határozza meg az időtartományt.
   2. Válassza ki a megfelelő eszközt.
   3. Szűrés **biztonsági mentési szabályzat** alapján a megfelelő biztonsági másolatok megtekintéséhez.
   3. A biztonsági mentési szabályzat legördülő listában válassza az **összes** lehetőséget a kijelölt eszközön található összes biztonsági mentés megtekintéséhez.
   4. A lekérdezés végrehajtásához kattintson az **alkalmaz** gombra.
      
      A kiválasztott biztonsági mentési szabályzathoz társított biztonsági másolatoknak szerepelniük kell a biztonságimásolat-készletek listájában.

      ![Ugrás a biztonsági mentési katalógusba](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

## <a name="select-a-backup-set"></a>Biztonságimásolat-készlet kiválasztása
A kötet-vagy biztonsági mentési szabályzathoz tartozó biztonságimásolat-készlet kiválasztásához hajtsa végre az alábbi lépéseket.

#### <a name="to-select-a-backup-set"></a>Biztonságimásolat-készlet kiválasztása
1. Lépjen a StorSimple Eszközkezelő szolgáltatásra, és kattintson a **Backup Catalog** elemre.
2. A következő módon szűrheti a beállításokat:
   
   1. Határozza meg az időtartományt. 
   2. Válassza ki a megfelelő eszközt. 
   3. A kijelölni kívánt biztonsági mentésre vonatkozó mennyiségi vagy biztonsági mentési szabályzat alapján szűrhet.
   4. A lekérdezés végrehajtásához kattintson az **alkalmaz** gombra.
      
      A kiválasztott kötethez vagy biztonsági mentési szabályzathoz társított biztonsági másolatoknak szerepelniük kell a biztonságimásolat-készletek listájában.

      ![Ugrás a 2. biztonsági mentési katalógusra](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Válassza ki és bontsa ki a biztonságimásolat-készletet. Most már megtekintheti a biztonságimásolat-készleteket a benne foglalt kötetek alapján. A **visszaállítási** és **törlési** beállítások a biztonságimásolat-készlet helyi menüjében (a jobb gombbal kattintva) érhetők el. Ezen műveletek bármelyikét elvégezheti a kiválasztott biztonságimásolat-készleten.

    ![Ugrás a biztonsági mentési katalógusra 3](./media/storsimple-8000-manage-backup-catalog/bucatalog2.png)

## <a name="delete-a-backup-set"></a>Biztonságimásolat-készlet törlése
Törölje a biztonsági mentést, ha már nem kívánja megőrizni a hozzá társított adatmennyiséget. A biztonságimásolat-készlet törléséhez hajtsa végre az alábbi lépéseket.

#### <a name="to-delete-a-backup-set"></a>Biztonságimásolat-készlet törlése
 Lépjen a StorSimple Eszközkezelő szolgáltatásra, és kattintson a **Backup Catalog** elemre.
1. A következő módon szűrheti a beállításokat:
   
   1. Határozza meg az időtartományt. 
   2. Válassza ki a megfelelő eszközt. 
   3. A kijelölni kívánt biztonsági mentésre vonatkozó mennyiségi vagy biztonsági mentési szabályzat alapján szűrhet.
   4. A lekérdezés végrehajtásához kattintson az **alkalmaz** gombra.
      
      A kiválasztott kötethez vagy biztonsági mentési szabályzathoz társított biztonsági másolatoknak szerepelniük kell a biztonságimásolat-készletek listájában.

      ![Ugrás a biztonsági mentési katalógusra 4](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

1. Válassza ki és bontsa ki a biztonságimásolat-készletet. Most már megtekintheti a biztonságimásolat-készleteket a benne foglalt kötetek alapján. A **visszaállítási** és **törlési** beállítások a biztonságimásolat-készlet helyi menüjében (a jobb gombbal kattintva) érhetők el. Kattintson a jobb gombbal a kiválasztott biztonságimásolat-készletre, és a helyi menüben válassza a **Törlés** lehetőséget.

    ![Ugrás a Backup katalógus 5](./media/storsimple-8000-manage-backup-catalog/bucatalog3.png)

1. Ha a rendszer megerősítést kér, tekintse át a megjelenített információkat, majd kattintson a **Törlés** gombra. A kijelölt biztonsági másolat véglegesen törlődik.

    ![Lépjen a Backup Catalog 6](./media/storsimple-8000-manage-backup-catalog/bucatalog4.png)  

1. A rendszer értesítést küld, ha a törlés folyamatban van, és sikeres volt a Befejezés. A törlés befejezése után frissítse a lekérdezést ezen az oldalon. A törölt biztonságimásolat-készlet többé nem jelenik meg a biztonságimásolat-készletek listájában.

    ![Ugrás a Backup Catalog 7-re](./media/storsimple-8000-manage-backup-catalog/bucatalog7.png)

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [állíthatja vissza az eszközt egy biztonságimásolat-készletből a biztonsági mentési katalógus használatával](storsimple-8000-restore-from-backup-set-u2.md).
* Ismerje meg, hogyan kezelheti a [StorSimple-eszközt a StorSimple Eszközkezelő szolgáltatással](storsimple-8000-manager-service-administration.md).

