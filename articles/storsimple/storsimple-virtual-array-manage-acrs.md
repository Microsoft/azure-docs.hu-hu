---
title: StorSimple virtuális tömb hozzáférés-vezérlési rekordjainak kezelése | Microsoft Docs
description: Ismerteti, hogyan kezelhető a hozzáférés-vezérlési rekordok (ACR-EK) annak meghatározásához, hogy mely gazdagépek kapcsolódhatnak egy kötethez a StorSimple virtuális tömbben.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: e154bb4f-faab-4d92-a593-900c3ddc9595
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bad0d7adfd77dff53b1582e63a91f2cd87a9233d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85507619"
---
# <a name="use-storsimple-device-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>StorSimple Eszközkezelő használata a StorSimple virtuális tömb hozzáférés-vezérlési rekordjainak kezeléséhez

## <a name="overview"></a>Áttekintés

A hozzáférés-vezérlési rekordok (ACR-EK) lehetővé teszik annak megadását, hogy mely gazdagépek kapcsolódhatnak egy kötethez a StorSimple virtuális tömbben (más néven a helyszíni virtuális eszköz StorSimple). A ACR-EK egy adott kötetre vannak beállítva, és tartalmazzák a gazdagépek iSCSI minősített nevét (IQNs). Amikor egy gazdagép megpróbál csatlakozni egy kötethez, az eszköz ellenőrzi az adott kötethez társított ACR-t a IQN nevéhez, és ha van egyezés, akkor létrejön a kapcsolat. A Eszközkezelő szolgáltatás **konfiguráció** szakaszában található **hozzáférés-vezérlési rekordok** panel megjeleníti az összes hozzáférés-vezérlési rekordot a gazdagépek megfelelő IQNs.

![Hozzáférés-vezérlési rekordok kezelése](./media/storsimple-virtual-array-manage-acrs/ova-manage-acrs.png)

Ez az oktatóanyag a következő általános ACR-feladatokat ismerteti:

* A IQN beolvasása
* Hozzáférés-vezérlési rekord hozzáadása
* Hozzáférés-vezérlési rekord szerkesztése
* Hozzáférés-vezérlési rekord törlése

> [!IMPORTANT]
> 
> * Ha az ACR-t kötethez rendeli, ügyeljen arra, hogy a kötetet ne használja egyidejűleg egynél több nem fürtözött gazdagép, mert ez megsértheti a kötetet.
> * Ha az ACR-t egy kötetről törli, győződjön meg arról, hogy a megfelelő gazdagép nem fér hozzá a kötethez, mert a törlés írási és olvasási megszakítást eredményezhet.


## <a name="get-the-iqn"></a>A IQN beolvasása

Hajtsa végre a következő lépéseket egy Windows Server 2012 rendszert futtató Windows-állomás IQN beszerzéséhez.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>ACR hozzáadása

A **hozzáférés-vezérlési rekordok** panelen a StorSimple Eszközkezelő szolgáltatásának **konfigurációs** szakaszán belül ACR-EK adhat hozzá. Általában egyetlen kötethez társít egy ACR-t.

Az ACR kötettel való társításával kapcsolatos információkért lépjen a [kötet hozzáadása](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume)lehetőségre.

> [!IMPORTANT]
> Ha az ACR-t kötethez rendeli, ügyeljen arra, hogy a kötetet ne használja egyidejűleg egynél több nem fürtözött gazdagép, mert ez megsértheti a kötetet.


Az ACR hozzáadásához hajtsa végre az alábbi lépéseket.

#### <a name="to-add-an-acr"></a>ACR hozzáadása

1. A szolgáltatás kezdőlapján válassza ki a szolgáltatást, kattintson duplán a szolgáltatás nevére, majd a **konfiguráció** szakaszban kattintson a **hozzáférés-vezérlési rekordok** elemre.
2. A **hozzáférés-vezérlési rekordok** panelen kattintson a **Hozzáadás** gombra.
3. Az **ACR hozzáadása** panelen tegye a következőket:
   
    1. Adja meg az ACR **nevét**.
    
    2. Az **iSCSI-kezdeményező neve** alatt adja meg a Windows-gazdagép IQN nevét. A Windows Server-gazdagép IQN beszerzéséhez tegye a következőket:
   
    3. Indítsa el a Microsoft iSCSI-kezdeményezőt a Windows-gazdagépen. Az iSCSI-kezdeményező tulajdonságai ablakban a **Konfiguráció** lapon jelölje ki, majd másolja a **Kezdeményező neve** mezőben lévő sztringet.
    Illessze be ezt a karakterláncot az **ACR hozzáadása** panel **IQN** mezőjébe.
   
    6. Az ACR hozzáadásához kattintson a **Hozzáadás** gombra.  
   
        ![Hozzáférés-vezérlési rekordok hozzáadása](./media/storsimple-virtual-array-manage-acrs/ova-add-acrs.png)
4. A táblázatos lista frissül, hogy tükrözze ezt a kiegészítést.

## <a name="edit-an-acr"></a>ACR szerkesztése

A ACR-EK szerkesztéséhez használja a Eszközkezelő szolgáltatásának **konfiguráció** szakaszában található **hozzáférés-vezérlési rekordok** panelt a Azure Portal.

> [!NOTE]
> Ne módosítson olyan ACR-t, amely jelenleg használatban van. A jelenleg használatban lévő kötethez társított ACR szerkesztéséhez először offline állapotba kell helyeznie a kötetet.


Az ACR szerkesztéséhez hajtsa végre az alábbi lépéseket.

#### <a name="to-edit-an-acr"></a>ACR szerkesztése

1. A szolgáltatás kezdőlapján válassza ki a szolgáltatást, kattintson duplán a szolgáltatás nevére, majd a **konfiguráció** szakaszban a **hozzáférés-vezérlési rekordok** elemre.
2. A **hozzáférés-vezérlési rekordok** panelen, a hozzáférés-vezérlési rekordok táblázatos listájából kattintson duplán a módosítani kívánt ACR elemre.
3. A **hozzáférés-vezérlési rekordok szerkesztése** panelen tegye a következőket:
   
    1. Adja meg az ACR IQN.
   
    2. Kattintson a panel tetején található **Mentés** gombra a módosított ACR mentéséhez. A következő megerősítő üzenet jelenik meg:
   
        ![Hozzáférés-vezérlési rekordok szerkesztése](./media/storsimple-virtual-array-manage-acrs/ova-edit-acrs.png)

## <a name="delete-an-access-control-record"></a>Hozzáférés-vezérlési rekord törlése

A ACR-EK törléséhez használja a Azure Portal **konfigurációs** lapját.

> [!NOTE]
> 
> * Ne töröljön olyan ACR-t, amely jelenleg használatban van. A jelenleg használatban lévő kötethez társított ACR törléséhez először offline állapotba kell helyeznie a kötetet.
> * Ha az ACR-t egy kötetről törli, győződjön meg arról, hogy a megfelelő gazdagép nem fér hozzá a kötethez, mert a törlés írási és olvasási megszakítást eredményezhet.


Egy hozzáférés-vezérlési rekord törléséhez hajtsa végre a következő lépéseket.

#### <a name="to-delete-an-access-control-record"></a>Hozzáférés-vezérlési rekord törlése

1. A szolgáltatás kezdőlapján válassza ki a szolgáltatást, kattintson duplán a szolgáltatás nevére, majd a **konfiguráció** szakaszban a **hozzáférés-vezérlési rekordok** elemre.

2. A **hozzáférés-vezérlési rekordok** panelen a hozzáférés-vezérlési rekordok táblázatos listájából kattintson duplán a törölni kívánt ACR elemre.

3. A hozzáférés-vezérlési rekordok szerkesztése panelen kattintson a **Törlés** elemre.
   
    ![ACR-EK törlése](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs.png)

4. Ha a rendszer megerősítést kér, kattintson a **Törlés** gombra a törlés folytatásához. A táblázatos lista frissül, hogy tükrözze a törlést.
   
   ![Figyelmeztető üzenet](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs-warning.png)

## <a name="next-steps"></a>Következő lépések

* További információ a [kötetek hozzáadásáról és a ACR-EK konfigurálásáról](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

