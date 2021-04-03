---
title: Hozzáférés-vezérlési rekordok kezelése a StorSimple-ben | Microsoft Docs
description: Ismerteti, hogyan használható a hozzáférés-vezérlési rekordok (ACR-EK) annak meghatározásához, hogy mely gazdagépek kapcsolódhatnak a StorSimple-eszközön található kötethez.
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
ms.date: 05/31/2017
ms.author: alkohli
ms.openlocfilehash: 15c35fb314af27b1ced129a12f752d0a2794e0f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91949904"
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>A hozzáférés-vezérlési rekordok kezelése a StorSimple Manager szolgáltatással

## <a name="overview"></a>Áttekintés
A hozzáférés-vezérlési rekordok (ACR-EK) lehetővé teszik annak megadását, hogy mely gazdagépek kapcsolódhatnak a StorSimple-eszközön található kötethez. A ACR-EK egy adott kötetre vannak beállítva, és tartalmazzák a gazdagépek iSCSI minősített nevét (IQNs). Amikor egy gazdagép megpróbál csatlakozni egy kötethez, az eszköz ellenőrzi az adott kötethez társított ACR-t a IQN nevéhez, és ha van egyezés, akkor a kapcsolat létrejött. A StorSimple Eszközkezelő szolgáltatás paneljének **konfigurációs** szakaszában található hozzáférés-vezérlési rekordok az összes hozzáférés-vezérlési rekordot megjelenítik a gazdagépek megfelelő IQNs.

Ez az oktatóanyag a következő általános ACR-feladatokat ismerteti:

* Hozzáférés-vezérlési rekord hozzáadása
* Hozzáférés-vezérlési rekord szerkesztése
* Hozzáférés-vezérlési rekord törlése

> [!IMPORTANT]
> * Ha az ACR-t kötethez rendeli, ügyeljen arra, hogy a kötetet ne használja egyidejűleg egynél több nem fürtözött gazdagép, mert ez megsértheti a kötetet.
> * Ha az ACR-t egy kötetről törli, győződjön meg arról, hogy a megfelelő gazdagép nem fér hozzá a kötethez, mert a törlés írási és olvasási megszakítást eredményezhet.

## <a name="get-the-iqn"></a>A IQN beolvasása

Hajtsa végre a következő lépéseket egy Windows Server 2012 rendszert futtató Windows-állomás IQN beszerzéséhez.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]


## <a name="add-an-access-control-record"></a>Hozzáférés-vezérlési rekord hozzáadása
A ACR-EK hozzáadásához használja a StorSimple Eszközkezelő szolgáltatás paneljének **konfigurációs** szakaszát. Jellemzően egy ACR-t társít egy kötethez.

Az ACR hozzáadásához hajtsa végre az alábbi lépéseket.

#### <a name="to-add-an-acr"></a>ACR hozzáadása

1. Lépjen a StorSimple Eszközkezelő szolgáltatásra, és kattintson duplán a szolgáltatás nevére, majd a **konfiguráció** szakaszban található hozzáférés- **vezérlési rekordok** elemre.
2. A **hozzáférés-vezérlési rekordok** panelen kattintson az **+ ACR hozzáadása** lehetőségre.

    ![Kattintson az ACR hozzáadása elemre.](./media/storsimple-8000-manage-acrs/createacr1.png)

3. Az **ACR hozzáadása** panelen hajtsa végre a következő lépéseket:

    1. Adja meg az ACR nevét.
    
    2. Adja meg a Windows Server-gazdagép IQN nevét az **iSCSI-kezdeményező neve (IQN)** területen.

    3. Az ACR létrehozásához kattintson a **Hozzáadás** gombra.

        ![Kattintson az ACR 2 hozzáadása lehetőségre.](./media/storsimple-8000-manage-acrs/createacr2.png)

4.  Az újonnan hozzáadott ACR a ACR-EK táblázatos listájában jelenik meg.

    ![Kattintson az ACR 3 hozzáadása elemre.](./media/storsimple-8000-manage-acrs/createacr5.png)


## <a name="edit-an-access-control-record"></a>Hozzáférés-vezérlési rekord szerkesztése
A ACR-EK szerkesztéséhez használja a StorSimple Eszközkezelő szolgáltatás paneljének **konfigurációs** szakaszát.

> [!NOTE]
> Javasoljuk, hogy csak azokat a ACR-EK módosítsa, amelyek jelenleg nincsenek használatban. A jelenleg használatban lévő kötethez társított ACR szerkesztéséhez először offline állapotba kell helyeznie a kötetet.

Az ACR szerkesztéséhez hajtsa végre az alábbi lépéseket.

#### <a name="to-edit-an-access-control-record"></a>Hozzáférés-vezérlési rekord szerkesztése
1.  Lépjen a StorSimple Eszközkezelő szolgáltatásra, és kattintson duplán a szolgáltatás nevére, majd a **konfiguráció** szakaszban található hozzáférés- **vezérlési rekordok** elemre.

    ![Hozzáférés-vezérlési rekordok keresése](./media/storsimple-8000-manage-acrs/createacr1.png)

2. A hozzáférés-vezérlési rekordok táblázatos listájában kattintson és válassza ki a módosítani kívánt ACR-t.

    ![Hozzáférés-vezérlési rekordok szerkesztése](./media/storsimple-8000-manage-acrs/editacr1.png)

3. A **hozzáférés-vezérlési rekord szerkesztése** panelen adjon meg egy másik IQN, amely egy másik gazdagépnek felel meg.

    ![Hozzáférés-vezérlési rekordok szerkesztése 2](./media/storsimple-8000-manage-acrs/editacr2.png)

4. Kattintson a **Mentés** gombra. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra. 

    ![Hozzáférés-vezérlési rekordok szerkesztése 3](./media/storsimple-8000-manage-acrs/editacr3.png)

5. Az ACR frissítésekor értesítést kap. A táblázatos lista szintén frissíti a változást.

   
## <a name="delete-an-access-control-record"></a>Hozzáférés-vezérlési rekord törlése
A ACR-EK törléséhez használja a StorSimple Eszközkezelő szolgáltatás paneljének **konfigurációs** szakaszát.

> [!NOTE]
> Csak azokat a ACR-EK lehet törölni, amelyek jelenleg nincsenek használatban. A jelenleg használatban lévő kötethez társított ACR törléséhez először offline állapotba kell helyeznie a kötetet.

Egy hozzáférés-vezérlési rekord törléséhez hajtsa végre a következő lépéseket.

#### <a name="to-delete-an-access-control-record"></a>Hozzáférés-vezérlési rekord törlése
1.  Lépjen a StorSimple Eszközkezelő szolgáltatásra, és kattintson duplán a szolgáltatás nevére, majd a **konfiguráció** szakaszban található hozzáférés- **vezérlési rekordok** elemre.

    ![Ugrás az 1b hozzáférés-vezérlési rekordokra](./media/storsimple-8000-manage-acrs/createacr1.png)

2. A hozzáférés-vezérlési rekordok táblázatos listájában kattintson és válassza ki a törölni kívánt ACR-t.

    ![Ugrás a hozzáférés-vezérlési rekordokra 2](./media/storsimple-8000-manage-acrs/deleteacr1.png)

3. Kattintson a jobb gombbal a helyi menü meghívásához, majd válassza a **Törlés** lehetőséget.

    ![Ugrás a hozzáférés-vezérlési rekordokra 3](./media/storsimple-8000-manage-acrs/deleteacr2.png)

4. Ha a rendszer megerősítést kér, tekintse át az adatokat, majd kattintson a **Törlés** gombra.

    ![Ugrás a hozzáférés-vezérlési rekordokra 4](./media/storsimple-8000-manage-acrs/deleteacr3.png)

5. A törlés befejeződése után értesítést kap. A táblázatos lista frissül, hogy tükrözze a törlést.

    ![Ugrás a hozzáférés-vezérlési rekordokra 5](./media/storsimple-8000-manage-acrs/deleteacr5.png)

## <a name="next-steps"></a>Következő lépések
* További információ a [StorSimple-kötetek kezeléséről](storsimple-8000-manage-volumes-u2.md).
* További információ [a StorSimple Manager szolgáltatás a StorSimple-eszköz felügyeletéhez való használatáról](storsimple-8000-manager-service-administration.md).

