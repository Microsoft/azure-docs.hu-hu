---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 560c9c177bfa693580979101e5b9343fcff7fe40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "67179315"
---
### <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Karbantartási mód frissítéseinek telepítése Windows PowerShell StorSimple-bővítménye használatával

Amikor karbantartási mód frissítéseit alkalmazza a StorSimple-eszközre, az összes I/O-kérés szünetel. Az olyan szolgáltatások, mint például a nem felejtő véletlen hozzáférésű memória (NVRAM) vagy a fürtszolgáltatási szolgáltatás le vannak állítva. Mindkét vezérlő újraindul, ha beírja vagy kizárja ezt a módot. Ha kizárja ezt a módot, az összes szolgáltatás folytatódik és kifogástalan állapotú. (Ez eltarthat néhány percig.)

> [!IMPORTANT]
> * A karbantartási mód megadása előtt ellenőrizze, hogy mindkét eszköz kifogástalan állapotban van-e a Azure Portal. Ha a vezérlő nem kifogástalan, [forduljon Microsoft ügyfélszolgálata](../articles/storsimple/storsimple-8000-contact-microsoft-support.md) a következő lépésekhez.
> * Karbantartási módban először frissítenie kell egy vezérlőt, majd a másik vezérlőt.

1. A PuTTY használatával csatlakozhat a soros konzolhoz. Kövesse [a PuTTY a soros konzolhoz való csatlakozáshoz történő használatát](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console) ismertető részletes útmutatásokat. A parancssorban nyomja le az **Enter** billentyűt. Válassza az 1. lehetőséget, majd **Jelentkezzen be teljes hozzáféréssel**.

2. A vezérlő karbantartási módba helyezéséhez írja be a következőt:
    
    `Enter-HcsMaintenanceMode`

    Mindkét vezérlő újraindul karbantartási módba.

3. Telepítse a karbantartási mód frissítéseit. Típus:

    `Start-HcsUpdate`

    A rendszer megerősítést kér. A frissítések jóváhagyása után azok a vezérlőre lesznek telepítve, amelyre jelenleg van hozzáférése. A frissítések telepítése után a vezérlő újraindul.

4. A frissítések állapotának figyelése. Jelentkezzen be a társ-vezérlőbe, mert az aktuális vezérlő frissítése folyamatban van, és nem tudja feldolgozni a többi parancsot. Típus:

    `Get-HcsUpdateStatus`

    Ha az `RunInProgress` a `True` , a frissítés még folyamatban van. Ha `RunInProgress` Igen `False` , azt jelzi, hogy a frissítés befejeződött.

5. A lemez belső vezérlőprogram-frissítéseinek sikeres alkalmazása és a frissített vezérlő újraindítása után ellenőrizze a lemez belső vezérlőprogram-verzióját. A frissített vezérlőn írja be a következőt:

    `Get-HcsFirmwareVersion`
   
    A várt lemez belső vezérlőprogram-verziói a következők:  `XMGJ, XGEG, KZ50, F6C2, VR08, N003, 0107`

6. Lépjen ki a karbantartási módból. Írja be az alábbi parancsot minden eszköz-vezérlőhöz:

    `Exit-HcsMaintenanceMode`

    A vezérlők újraindulnak, miután kilépett a karbantartási módból.

7. Lépjen vissza az Azure Portalra. Előfordulhat, hogy a portálon nem látható, hogy 24 óráig telepítette a karbantartási mód frissítéseit.