---
title: StorSimple-eszköz frissítése | Microsoft Docs
description: A cikk azt ismerteti, hogyan használható a StorSimple Update szolgáltatás a normál és a karbantartási mód frissítéseinek és gyorsjavításának telepítéséhez.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 786059f5-2a38-4105-941d-0860ce4ac515
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: faf06775b78c5a7c90cea000ac0a1eb768107ef4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94956840"
---
# <a name="update-your-storsimple-8000-series-device"></a>A StorSimple 8000 Series eszköz frissítése
> [!NOTE]
> A StorSimple klasszikus portálja elavult. A StorSimple-eszközkezelők automatikusan átkerülnek az új Azure Portalra az elavulási ütemezésnek megfelelően. Erről az áthelyezésről kapni fog egy e-mailt és egy Portal-értesítést. Ez a dokumentum hamarosan el lesz távolítva. Ha kérdései vannak az áthelyezéssel kapcsolatban, tekintse meg a [Gyakori kérdések: Váltás az Azure Portalra](./index.yml) szakaszt.

## <a name="overview"></a>Áttekintés
A StorSimple-frissítések funkció lehetővé teszi a StorSimple-eszköz naprakészen tartását. A frissítési típustól függően a klasszikus Azure portálon vagy a Windows PowerShell felületén keresztül is alkalmazhat frissítéseket az eszközre. Ez az oktatóanyag ismerteti a frissítési típusokat és azok telepítésének módját.

Két típusú eszköz-frissítést is alkalmazhat: 

* Rendszeres (vagy normál módú) frissítések
* Karbantartási mód frissítései

A hagyományos frissítéseket a klasszikus Azure portálon vagy a Windows PowerShellen keresztül is telepítheti. a karbantartási mód frissítéseinek telepítéséhez azonban a Windows PowerShellt kell használnia. 

Az egyes frissítési típusok külön vannak leírva.

### <a name="regular-updates"></a>Rendszeres frissítések
A rendszeres frissítések olyan nem zavaró frissítések, amelyek akkor telepíthetők, ha az eszköz normál módban van. Ezeket a frissítéseket a Microsoft Update webhelyén végezheti el az egyes eszközök vezérlőjén. 

> [!IMPORTANT]
> A frissítési folyamat során a vezérlő feladatátvétele is előfordulhat. Ez azonban nem befolyásolja a rendszer rendelkezésre állását vagy működését.
> 
> 

* A normál frissítések a klasszikus Azure-portálon keresztül történő telepítésével kapcsolatos részletekért lásd [a normál frissítések telepítése a klasszikus Azure portálon keresztül](#install-regular-updates-via-the-azure-classic-portal)című témakört.
* A rendszeres frissítéseket Windows PowerShell StorSimple-bővítménye használatával is telepítheti. Részletekért lásd: a [rendszeres frissítések telepítése Windows PowerShell StorSimple-bővítménye használatával](#install-regular-updates-via-windows-powershell-for-storsimple).

### <a name="maintenance-mode-updates"></a>Karbantartási mód frissítései
A karbantartási mód frissítései zavaró frissítések, például a lemezes belső vezérlőprogram frissítései. Ezek a frissítések megkövetelik, hogy az eszköz karbantartási módba kerüljön. Részletekért lásd [: 2. lépés: a karbantartási mód megadása](#step2). A klasszikus Azure portál nem használható a karbantartási mód frissítéseinek telepítéséhez. Ehelyett a Windows PowerShell StorSimple-bővítményet kell használnia. 

A karbantartási mód frissítéseinek telepítésével kapcsolatos részletekért lásd: a [karbantartási mód frissítéseinek telepítése Windows PowerShell StorSimple-bővítménye használatával](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [!IMPORTANT]
> A karbantartási mód frissítéseit külön kell alkalmazni az egyes vezérlőkön. 
> 
> 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>Normál frissítések telepítése a klasszikus Azure portálon keresztül
A klasszikus Azure portál használatával frissítéseket alkalmazhat a StorSimple-eszközre.

[!INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>Rendszeres frissítések telepítése Windows PowerShell StorSimple-bővítménye használatával
Azt is megteheti, hogy a Windows PowerShell StorSimple-bővítménye használatával normál (normál módú) frissítéseket alkalmazhat.

> [!IMPORTANT]
> Bár a Windows PowerShell StorSimple-bővítménye használatával is telepítheti a rendszeres frissítéseket, javasoljuk, hogy a klasszikus Azure portálon telepítse a rendszeres frissítéseket. Az 1. frissítéstől kezdve a frissítések a portálról történő telepítése előtt a rendszer előzetes ellenőrzéseket hajt végre. Ezek az előzetes ellenőrzések megelőzik a hibákat, és zökkenőmentesebb élményt biztosítanak. 
> 
> 

[!INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Karbantartási mód frissítéseinek telepítése Windows PowerShell StorSimple-bővítménye használatával
A karbantartási mód frissítéseinek a StorSimple-eszközre való alkalmazásához Windows PowerShell StorSimple-bővítménye használ. Az összes I/O-kérés szünetel ebben a módban. Az olyan szolgáltatások, mint például a nem felejtő véletlenszerű hozzáférésű memória (NVRAM) vagy a fürtszolgáltatási szolgáltatás is leállnak. Ha beírja vagy kizárja ezt a módot, mindkét vezérlő újraindul. Ha kizárja ezt a módot, az összes szolgáltatás folytatódik, és kifogástalannak kell lennie. (Ez eltarthat néhány percig.)

Ha karbantartási mód frissítéseit kell alkalmaznia, a klasszikus Azure portálon keresztül riasztást kap, amelyre telepíteni kell a frissítéseket. Ez a riasztás a frissítések telepítéséhez Windows PowerShell StorSimple-bővítménye használatára vonatkozó utasításokat tartalmaz. Az eszköz frissítése után ugyanazzal az eljárással módosíthatja az eszközt a normál üzemmódra. Részletes útmutatásért lásd [: 4. lépés: Kilépés a karbantartási módból](#step4).

> [!IMPORTANT]
> * A karbantartási mód megadása előtt ellenőrizze, hogy mindkét eszköz állapota Kifogástalan-e, ha ellenőrzi a **hardver állapotát** a klasszikus Azure portál **karbantartás** lapján. Ha a vezérlő nem kifogástalan, forduljon Microsoft ügyfélszolgálata a következő lépésekhez. További információért lépjen a Contact Microsoft ügyfélszolgálata elemre. 
> * Ha karbantartási módban van, először az egyik vezérlőn, majd a másik vezérlőn kell alkalmaznia a frissítést.
> 
> 

### <a name="step-1-connect-to-the-serial-console"></a>1. lépés: Kapcsolódás a soros konzolhoz <a name="step1"></a>
Először használjon egy alkalmazást, például a PuTTY-t a soros konzol eléréséhez. Az alábbi eljárás azt ismerteti, hogyan használható a PuTTY a soros konzolhoz való kapcsolódáshoz.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode"></a>2. lépés: a karbantartási mód megadása <a name="step2"></a>
A konzolhoz való kapcsolódás után állapítsa meg, hogy vannak-e frissítések a telepítéshez, majd adja meg a karbantartási módot a telepítéshez.

[!INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates"></a>3. lépés: a frissítések telepítése <a name="step3"></a>
Ezután telepítse a frissítéseket.

[!INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]

### <a name="step-4-exit-maintenance-mode"></a>4. lépés: Kilépés a karbantartási módból <a name="step4"></a>
Végül zárja be a karbantartási módot.

[!INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>Gyorsjavítások telepítése Windows PowerShell StorSimple-bővítménye használatával
A Microsoft Azure StorSimple frissítéseitől eltérően a gyorsjavítások egy megosztott mappából lesznek telepítve. A frissítésekhez hasonlóan a gyorsjavítások két típusa létezik: 

* Rendszeres gyorsjavítások 
* Karbantartási mód gyorsjavításai  

A következő eljárások azt ismertetik, hogyan használhatók a Windows PowerShell StorSimple-bővítménye a normál és a karbantartási módú gyorsjavítások telepítéséhez.

[!INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[!INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>Mi történik a frissítésekkel, ha az eszköz gyári alaphelyzetbe állítását végzi?
Ha egy eszköz visszaáll a gyári beállításokra, akkor az összes frissítés elvész. A gyári beállítások visszaállítása után az eszköz regisztrálása és konfigurálása után manuálisan kell telepítenie a frissítéseket a klasszikus Azure portálon és/vagy Windows PowerShell StorSimple-bővítménye. A gyári beállítások visszaállításával kapcsolatos további információkért lásd: [az eszköz visszaállítása a gyári alapértékekre](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).

## <a name="next-steps"></a>Következő lépések
* További információ a [StorSimple-eszköz felügyeletének Windows PowerShell StorSimple-bővítménye használatáról](./storsimple-8000-windows-powershell-administration.md).
* További információ [a StorSimple Manager szolgáltatás a StorSimple-eszköz felügyeletéhez való használatáról](./storsimple-8000-manager-service-administration.md).