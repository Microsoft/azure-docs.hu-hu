---
title: StorSimple jelszavának módosítása | Microsoft Docs
description: Útmutatás a StorSimple Eszközkezelő szolgáltatás használatával a StorSimple Snapshot Manager és az eszköz rendszergazdai jelszavának módosításához.
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
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 038084ba9ae43e14bc2eb42bf258912be27d062c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023754"
---
# <a name="use-the-storsimple-device-manager-service-to-change-your-storsimple-passwords"></a>A StorSimple Eszközkezelő szolgáltatás használata a StorSimple jelszavának módosítására

## <a name="overview"></a>Áttekintés
A Azure Portal **eszközbeállítások** lehetőség az összes olyan eszköz-paramétert tartalmazza, amelyet egy StorSimple Eszközkezelő szolgáltatás által felügyelt StorSimple-eszközön konfigurálhat. Ez az oktatóanyag azt ismerteti, hogy miként használható az eszközbeállítások **alatt a** **biztonsági** lehetőség az eszköz rendszergazdája vagy StorSimple Snapshot Manager jelszavának módosításához.

## <a name="change-the-device-administrator-password"></a>Az eszköz rendszergazdai jelszavának módosítása
Ha Windows PowerShell-felületet használ a StorSimple-eszköz eléréséhez, meg kell adnia egy eszköz rendszergazdai jelszavát. Ha az első StorSimple-eszköz regisztrálva van egy szolgáltatásban, a csatoló alapértelmezett jelszava *jelszó1*. Az adatai biztonsága érdekében a regisztrációs folyamat végén meg kell változtatnia ezt a jelszót. A jelszó módosítása nélkül nem léphet ki a regisztrációs folyamatból. További információ [: 3. lépés: az eszköz konfigurálása és regisztrálása a Windows PowerShell StorSimple-bővítménye használatával](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

A regisztráció során a Windows PowerShell felületén először beállított jelszó később a Azure Portal használatával módosítható. Az eszköz rendszergazdai jelszavának módosításához hajtsa végre az alábbi lépéseket.

#### <a name="to-change-the-device-administrator-password"></a>Az eszköz rendszergazdai jelszavának módosítása
1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre.

2. Az eszközök táblázatos listájából válassza ki, majd kattintson arra az eszközre, amelynek a jelszavát módosítani kívánja.

    ![Képernyőfelvétel: a StorSimple Eszközkezelő szolgáltatás. A felügyelet területen válassza az eszközök lehetőséget. Az eszközök listájában egy eszköz van kiválasztva.](./media/storsimple-8000-change-passwords/changepwd1.png)

3. A **Beállítások** panelen válassza az **eszközbeállítások > biztonság lehetőséget**.

    ![A Eszközkezelő szolgáltatás beállítások paneljét ábrázoló képernyőfelvétel. Az eszközbeállítások területen válassza a biztonság lehetőséget.](./media/storsimple-8000-change-passwords/changepwd2.png)

4. A **biztonsági beállítások** panelen kattintson a **jelszó** lehetőségre az eszköz rendszergazdai jelszavának módosításához.

    ![A biztonsági beállítások panelt ábrázoló képernyőfelvétel A jelszó gomb ki van emelve.](./media/storsimple-8000-change-passwords/changepwd3.png)

5. A **jelszó** panelen adjon meg egy 8 – 15 karaktert tartalmazó rendszergazdai jelszót. A jelszónak 3 vagy több nagybetűből, kisbetűkből, számokból és speciális karakterekből álló kombinációnak kell lennie.

6. Erősítse meg a jelszót.

    ![A jelszó panelt ábrázoló képernyőfelvétel Az eszköz rendszergazdai jelszava alatt az új jelszó és a Jelszó megerősítése mezők ki lesznek töltve.](./media/storsimple-8000-change-passwords/changepwd4.png)

7. Kattintson a **Mentés** gombra, és amikor a rendszer megerősítést kér, kattintson az **Igen** gombra.

    ![A jelszó panelt ábrázoló képernyőfelvétel A Mentés gomb ki van emelve.](./media/storsimple-8000-change-passwords/changepwd6.png)

Most frissíteni kell az eszköz rendszergazdai jelszavát. Ezt a módosított jelszót használhatja a Windows PowerShell felületének eléréséhez.

## <a name="set-the-storsimple-snapshot-manager-password"></a>A StorSimple Snapshot Manager jelszavának beállítása
A StorSimple Snapshot Manager szoftver a Windows-állomáson található, és lehetővé teszi a rendszergazdák számára, hogy helyi és felhőalapú pillanatfelvételek formájában kezeljék a StorSimple eszköz biztonsági mentéseit.

Ha az eszközt a StorSimple Snapshot Manager konfigurálja, a rendszer kérni fogja az eszköz IP-címének és jelszavának megadását a tárolóeszköz hitelesítéséhez.

A StorSimple Snapshot Manager jelszavát a Azure Portal használatával állíthatja be vagy módosíthatja. A StorSimple Snapshot Manager jelszavának beállításához vagy módosításához hajtsa végre az alábbi lépéseket.

#### <a name="to-set-the-storsimple-snapshot-manager-password"></a>A StorSimple Snapshot Manager jelszavának beállítása
1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre.

2. Az eszközök táblázatos listájából válassza ki, majd kattintson arra az eszközre, amelynek a StorSimple meg kívánja határozni vagy módosítani szeretné a Snapshot Manager jelszavát.

     ![Képernyőfelvétel: a StorSimple Eszközkezelő szolgáltatás. A felügyelet területen válassza az eszközök lehetőséget. Az eszközök listájában egy eszköz van kiválasztva.](./media/storsimple-8000-change-passwords/changepwd1.png)

3. A **Beállítások** panelen válassza az **eszközbeállítások > biztonság lehetőséget**.

     ![A Eszközkezelő szolgáltatás beállítások paneljét ábrázoló képernyőfelvétel. Az eszközbeállítások területen válassza a biztonság lehetőséget.](./media/storsimple-8000-change-passwords/changepwd2.png)

4. A **biztonsági beállítások** panelen kattintson a **jelszó** gombra a StorSimple Snapshot Manager jelszavának beállításához vagy módosításához.

     ![A biztonsági beállítások panelt ábrázoló képernyőfelvétel A jelszó gomb ki van emelve.](./media/storsimple-8000-change-passwords/changepwd3.png) 

5. A **jelszó** panelen adjon meg egy 14 vagy 15 karakterből álló jelszót. Győződjön meg arról, hogy a jelszó 3 vagy több nagybetűt, kisbetűt, számot és speciális karaktert tartalmaz.

6. Erősítse meg a jelszót.

     ![A jelszó panelt ábrázoló képernyőfelvétel A jelszó Snapshot Manager alatt az új jelszó és a Jelszó megerősítése mezők ki lesznek töltve.](./media/storsimple-8000-change-passwords/changepwd5.png)

7. Kattintson a **Mentés** gombra, és amikor a rendszer megerősítést kér, kattintson az **Igen** gombra.

     ![A jelszó panelt ábrázoló képernyőfelvétel A Mentés gomb ki van emelve.](./media/storsimple-8000-change-passwords/changepwd6.png)

Most frissíteni kell a StorSimple Snapshot Manager jelszavát.

## <a name="next-steps"></a>Következő lépések
* További információ a [StorSimple biztonságáról](storsimple-8000-security.md).
* További információ az [eszköz konfigurációjának módosításáról](storsimple-8000-modify-device-config.md).
* További információ [a StorSimple Eszközkezelő szolgáltatás a StorSimple-eszköz felügyeletéhez való használatáról](storsimple-8000-manager-service-administration.md).

