---
title: StorSimple virtuális tömb eszköz rendszergazdai jelszavának módosítása | Microsoft Docs
description: Ismerteti, hogyan lehet a Azure Portal vagy a StorSimple virtuális tömb webes FELÜLETét használni az eszköz rendszergazdai jelszavának módosításához.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 11490814-d9fd-4dc7-9c3b-55dd2c23eaf1
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8bc8846d546faec194617ccb753cdbd105e16bf9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85513624"
---
# <a name="change-the-storsimple-virtual-array-device-administrator-password-via-storsimple-device-manager"></a>A StorSimple virtuális tömb eszköz rendszergazdai jelszavának módosítása a StorSimple-n keresztül Eszközkezelő

## <a name="overview"></a>Áttekintés

Ha a Windows PowerShell felületét használja a StorSimple virtuális tömb eléréséhez, meg kell adnia egy eszköz rendszergazdai jelszavát. Amikor a StorSimple-eszközt először kiépítik és elindították, az alapértelmezett jelszó a *jelszó1*. Az adatai biztonsága érdekében az alapértelmezett jelszó lejár az első bejelentkezéskor, és módosítania kell a jelszót.

A helyi webes felhasználói felületet vagy a Azure Portal is használhatja az eszköz rendszergazdai jelszavának módosítására az eszköz éles környezetbe való telepítése után. Ebben a cikkben a fenti eljárások mindegyikét ismertetjük.

 ![eszközök panel](./media/storsimple-virtual-array-change-device-admin-password/ova-devices-blade.png)

## <a name="use-the-azure-portal-to-change-the-password"></a>A jelszó megváltoztatásához használja a Azure Portal

A következő lépések végrehajtásával módosíthatja az eszköz rendszergazdai jelszavát a Azure Portalon keresztül.

#### <a name="to-change-the-device-administrator-password-via-the-azure-portal"></a>Az eszköz rendszergazdai jelszavának módosítása a Azure Portal használatával

1. A szolgáltatás kezdőlapján válassza ki a szolgáltatást, kattintson duplán a szolgáltatás nevére, majd a **felügyelet** szakaszban kattintson az **eszközök** elemre. Ekkor megnyílik az **eszközök** panel, amely felsorolja az összes StorSimple virtuális tömb eszközét.

2. Az **eszközök** panelen kattintson duplán arra az eszközre, amelyre a jelszó módosítása szükséges.

3. Az eszköz **Beállítások** paneljén kattintson a **Biztonság** elemre.

4. A **biztonsági beállítások** panelen tegye a következőket:
   
   1. Görgessen le az **eszköz rendszergazdai jelszava** szakaszhoz. Adjon meg egy 8 – 15 karaktert tartalmazó rendszergazdai jelszót.
   2. Erősítse meg a jelszót.
   3. Kattintson a panel tetején található **Mentés** gombra.

Az eszköz rendszergazdai jelszava most frissül. Ezt a módosított jelszót használhatja az eszköz helyi eléréséhez.

![Biztonsági beállítások panel](./media/storsimple-virtual-array-change-device-admin-password/ova-change-device-pwd.png)

## <a name="use-the-local-web-ui-to-change-the-password"></a>A jelszó megváltoztatásához használja a helyi webes felhasználói felületet.

Hajtsa végre az alábbi lépéseket az eszköz rendszergazdai jelszavának a helyi webes felületen való módosításához.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Az eszköz rendszergazdai jelszavának módosítása a helyi webes felületen keresztül

1. A helyi webes felhasználói felületen kattintson a **karbantartási**  >  **jelszó módosítása** elemre az eszközön.
   
    ![jelszó1 módosítása](./media/storsimple-virtual-array-change-device-admin-password/image40.png)
2. Adja meg az **aktuális jelszót**.
3. Adjon meg egy **új jelszót**. A jelszónak legalább 8 karakter hosszúnak kell lennie. A következő 3/4 értékből kell állnia: nagybetűk, kisbetűk, számok és speciális karakterek.
   
    Vegye figyelembe, hogy a jelszó nem egyezhet meg az utolsó 24 jelszóval.
4. Írja be ismét a jelszót a megerősítéséhez.
   
    ![password2 módosítása](./media/storsimple-virtual-array-change-device-admin-password/image41.png)
5. Az oldal alján kattintson az **alkalmaz** gombra. Az új jelszó már alkalmazva van. Ha a jelszó módosítása nem sikerült, a következő hibaüzenet jelenik meg:
   
    ![jelszavas hiba](./media/storsimple-virtual-array-change-device-admin-password/image42.png)
   
    A jelszó sikeres frissítése után értesítést kap. Ezután ezt a módosított jelszót használhatja az eszköz helyi eléréséhez.


## <a name="next-steps"></a>Következő lépések
Ismerje meg, hogyan [kezelheti a StorSimple virtuális tömböt](storsimple-ova-web-ui-admin.md).

