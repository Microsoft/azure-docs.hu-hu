---
title: StorSimple eszköz üzemmódjának módosítása | Microsoft Docs
description: Ismerteti a StorSimple eszköz üzemmódját, és ismerteti, hogyan lehet a Windows PowerShell StorSimple-bővítménye használatával módosítani az eszköz üzemmódját.
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
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: c7b0ea489c1d70ab86d677aad666ea6728fa76b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017083"
---
# <a name="change-the-device-mode-on-your-storsimple-device"></a>Az eszköz üzemmódjának módosítása a StorSimple-eszközön

Ez a cikk a StorSimple-eszköz működésének különböző módjairól nyújt rövid leírást. A StorSimple-eszköz háromféle módban működhet: normál, karbantartás és helyreállítás.

A cikk elolvasása után a következőket fogja tudni:

* A StorSimple-eszközök módjai
* A StorSimple-eszköz által használt üzemmód meghatározása
* Váltás normálról karbantartási módra és *fordítva*

A fenti felügyeleti feladatok csak a StorSimple-eszköz Windows PowerShell-felületén végezhetők el.

## <a name="about-storsimple-device-modes"></a>Tudnivalók a StorSimple eszköz módjairól

A StorSimple-eszköz normál, karbantartási vagy helyreállítási módban is működhet. Az alábbiakban röviden ismertetjük ezeket a módokat.

### <a name="normal-mode"></a>Normál mód

Ez a teljes körűen konfigurált StorSimple-eszköz normál működési módjaként van meghatározva. Alapértelmezés szerint az eszköznek normál üzemmódban kell lennie.

### <a name="maintenance-mode"></a>Karbantartási mód

Időnként előfordulhat, hogy a StorSimple eszközt karbantartási módba kell helyezni. Ez a mód lehetővé teszi az eszköz karbantartásának elvégzését, és a zavaró frissítések, például a lemezes belső vezérlőprogram esetében a telepítését.

A rendszerek csak a Windows PowerShell StorSimple-bővítményeon keresztül helyezhetők karbantartási módba. Az összes I/O-kérés szünetel ebben a módban. Az olyan szolgáltatások, mint például a nem felejtő véletlenszerű hozzáférésű memória (NVRAM) vagy a fürtszolgáltatási szolgáltatás is leállnak. A rendszer akkor is újraindítja a vezérlőket, amikor beírja vagy kizárja ezt a módot. Ha kilép a karbantartási módból, az összes szolgáltatás folytatódik, és kifogástalan állapotba kerül. Ez eltarthat néhány percig.

> [!NOTE]
> **A karbantartási mód csak megfelelően működő eszközön támogatott. Nem támogatott olyan eszközön, amelyben az egyik vagy mindkét vezérlő nem működik.**


### <a name="recovery-mode"></a>Helyreállítási mód

A helyreállítási mód a következő lehet: "Windows csökkentett mód hálózati támogatással". A helyreállítási mód bekapcsolja az Microsoft ügyfélszolgálata csapatot, és lehetővé teszi számukra a diagnosztika végrehajtását a rendszeren. A helyreállítási mód elsődleges célja a rendszernaplók beolvasása.

Ha a rendszer helyreállítási módba kerül, lépjen kapcsolatba Microsoft ügyfélszolgálata a következő lépésekhez. További információért lépjen a [Contact Microsoft ügyfélszolgálata](storsimple-8000-contact-microsoft-support.md)elemre.

> [!NOTE]
> **Az eszköz nem helyezhető helyreállítási módba. Ha az eszköz rossz állapotban van, a helyreállítási mód megkísérli beolvasni az eszközt olyan állapotba, amelyben Microsoft ügyfélszolgálata személyzet megvizsgálhatja.**

## <a name="determine-storsimple-device-mode"></a>StorSimple-eszköz üzemmódjának megállapítása

#### <a name="to-determine-the-current-device-mode"></a>Az eszköz aktuális üzemmódjának meghatározása

1. Jelentkezzen be az eszköz soros konzolján a [Putty használata az eszköz soros konzolhoz való kapcsolódáshoz](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)című témakör lépéseit követve.
2. Tekintse meg a szalagcím üzenetet az eszköz soros konzol menüjében. Ez az üzenet explicit módon jelzi, hogy az eszköz karbantartási vagy helyreállítási módban van-e. Ha az üzenet nem tartalmaz a rendszerüzemmódra vonatkozó konkrét adatokat, az eszköz normál módban van.

## <a name="change-the-storsimple-device-mode"></a>StorSimple eszköz üzemmódjának módosítása

A StorSimple-eszközt karbantartási módba helyezheti (normál módból) a karbantartáshoz vagy a karbantartási mód frissítéseinek telepítéséhez. A karbantartási mód megadásához vagy kilépéséhez hajtsa végre az alábbi lépéseket.

> [!IMPORTANT]
> A karbantartási mód megadása előtt ellenőrizze, hogy mindkét eszköz állapota Kifogástalan-e, ha az eszköz **beállításait > hardver állapotát** a Azure Portal. Ha az egyik vagy mindkét vezérlő állapota nem kifogástalan, forduljon Microsoft ügyfélszolgálata a következő lépésekhez. További információért lépjen a [Contact Microsoft ügyfélszolgálata](storsimple-8000-contact-microsoft-support.md)elemre.
 

#### <a name="to-enter-maintenance-mode"></a>Karbantartási mód megadása

1. Jelentkezzen be az eszköz soros konzolján a [Putty használata az eszköz soros konzolhoz való kapcsolódáshoz](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)című témakör lépéseit követve.
2. A soros konzol menüjében válassza az 1. lehetőséget, majd **Jelentkezzen be a teljes hozzáférés** lehetőséggel. Ha a rendszer kéri, adja meg az **eszköz rendszergazdai jelszavát**. Az alapértelmezett jelszó: `Password1` .
3. A parancssorba írja be a következőt: 
   
    `Enter-HcsMaintenanceMode`
4. Megjelenik egy figyelmeztető üzenet, amely közli, hogy a karbantartási mód megszakítja az összes I/O-kérést, és leválasztja a Azure Portalhoz való kapcsolódást, és a rendszer megerősítést kér. A karbantartási mód megadásához írja be az **Y karaktert** .
5. Mindkét vezérlő újra fog indulni. Az újraindítás befejezésekor a soros konzol szalagcíme jelzi, hogy az eszköz karbantartási módban van. Az alábbiakban egy példa látható a kimenetre.

```
    ---------------------------------------------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------

    Controller0>Enter-HcsMaintenanceMode
    Checking device state...

    In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y

    <BOTH CONTROLLERS RESTART>

    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------

    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>

```

#### <a name="to-exit-maintenance-mode"></a>A karbantartási mód kilépése

1. Jelentkezzen be az eszköz soros konzolján. Ellenőrizze az eszköz karbantartási módban lévő szalagcímét.
2. A parancssorba írja be a következőt:
   
    `Exit-HcsMaintenanceMode`
3. Megjelenik egy figyelmeztető üzenet, és egy megerősítési üzenet jelenik meg. A karbantartási módból való kilépéshez írja be az **Y karaktert** .
4. Mindkét vezérlő újra fog indulni. Ha az újraindítás befejeződött, a soros konzol szalagcíme azt jelzi, hogy az eszköz normál módban van. Az alábbiakban egy példa látható a kimenetre.

```
    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0
    ---------------------------------------------------------------

    Controller0>Exit-HcsMaintenanceMode
    Checking device state...

    Before exiting maintenance mode, ensure that any updates that are required on both controllers have been applied. Failure to install on each controller could result in data corruption. Exiting maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to exit maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y

    <BOTH CONTROLLERS RESTART>

    ---------------------------------------------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Active
    ---------------------------------------------------------------

    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>
```

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [alkalmazhat normál és karbantartási üzemmódú frissítéseket](storsimple-update-device.md) a StorSimple-eszközön.

