---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 2d0fd904dd704c704662192e1e92fe403f0971c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "67179149"
---
#### <a name="to-install-maintenance-mode-hotfixes-via-windows-powershell-for-storsimple"></a>A karbantartási módú gyorsjavítások telepítése Windows PowerShell StorSimple-bővítménye használatával
> [!IMPORTANT]
> Karbantartási módban először az egyik vezérlőn, majd a másik vezérlőn kell alkalmaznia a gyorsjavítást.
> 
> 

1. Helyezze az eszközt karbantartási módba. A karbantartási mód megadására vonatkozó utasításokért lásd a [2. lépés: a karbantartási mód megadása](../articles/storsimple/storsimple-update-device.md#step2) című témakört.
2. A gyorsjavítás alkalmazásához írja be a következőt:
   
     `Start-HcsHotfix` 
3. Ha a rendszer kéri, adja meg a gyorsjavítási fájlokat tartalmazó megosztott hálózati mappa elérési útját.
4. A rendszer a művelet megerősítését kéri. A gyorsjavítás telepítésének folytatásához írja be az **Y karaktert** .
5. Miután alkalmazta a gyorsjavítást egy vezérlőn, jelentkezzen be a másik vezérlőbe. Alkalmazza a gyorsjavítást az előző vezérlőhöz.
6. A gyorsjavítások alkalmazása után lépjen ki a karbantartási módból. Lásd: [4. lépés: Kilépés a karbantartási módból](../articles/storsimple/storsimple-update-device.md#step4) az utasításokhoz.

