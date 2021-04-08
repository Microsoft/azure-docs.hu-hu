---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 720288aff462b0590bb9da509096a9305b9b6cc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "67179154"
---
#### <a name="to-install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>A karbantartási mód frissítéseinek telepítése Windows PowerShell StorSimple-bővítménye használatával
1. Ha még nem tette meg, nyissa meg az eszköz soros konzolját, és válassza az 1. lehetőséget, **Jelentkezzen be a teljes hozzáférés** lehetőséggel. 
2. Írja be a jelszót. Az alapértelmezett jelszó a **jelszó1**.
3. A parancssorba írja be a következőt:
   
     `Get-HcsUpdateAvailability` 
4. Értesítést kap, ha elérhetők a frissítések, és hogy a frissítések zavaróak vagy nem zavaróak-e. A zavaró frissítések alkalmazásához az eszközt karbantartási módba kell helyeznie. Lásd: [2. lépés: adja meg a karbantartási módot](../articles/storsimple/storsimple-update-device.md#step2) az utasításokhoz.
5. Ha az eszköz karbantartási módban van, a parancssorba írja be a következőt: `Start-HcsUpdate`
6. A rendszer a művelet megerősítését kéri. A frissítések megerősítése után a rendszer a jelenleg használt vezérlőre telepíti őket. A frissítések telepítése után a vezérlő újra fog indulni. 
7. A frissítések állapotának figyelése. Típus:
   
    `Get-HcsUpdateStatus`
   
    Ha az `RunInProgress` a `True` , a frissítés még folyamatban van. Ha `RunInProgress` Igen `False` , azt jelzi, hogy a frissítés befejeződött.  
8. Ha a frissítés telepítve van az aktuális vezérlőn, és újraindul, kapcsolódjon a másik vezérlőhöz, és hajtsa végre az 1 – 6. lépést.
9. Mindkét vezérlő frissítése után lépjen ki a karbantartási módból. Lásd: [4. lépés: Kilépés a karbantartási módból](../articles/storsimple/storsimple-update-device.md#step4) az utasításokhoz.

