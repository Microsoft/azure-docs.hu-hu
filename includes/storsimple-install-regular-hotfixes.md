---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8cc5dbb907c342b766cebe6da36cf580ddac5e2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "67179151"
---
#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>A rendszeres gyorsjavítások telepítése Windows PowerShell StorSimple-bővítménye használatával
1. Kapcsolódjon az eszköz soros konzolhoz. További információt az 1. [lépés: Kapcsolódás a soros konzolhoz](../articles/storsimple/storsimple-update-device.md#step1)című témakörben talál.
2. A soros konzol menüjében válassza az 1. lehetőséget, majd **Jelentkezzen be a teljes hozzáférés** lehetőséggel. Írja be a jelszót. Az alapértelmezett jelszó a **jelszó1**.
3. A parancssorba írja be a következőt:
   
    ```
    Start-HcsHotfix
    ```
   
    > [!IMPORTANT]
    >
    > Ez a parancs csak a rendszeres gyorsjavításokra vonatkozik. Ezt a parancsot csak egy vezérlőn futtatja, de a rendszer mindkét vezérlőt frissíti.
    > A frissítési folyamat során észreveheti, hogy a vezérlő feladatátvételt végez; a feladatátvétel azonban nem fogja érinteni a rendszer rendelkezésre állását vagy működését.

4. Ha a rendszer kéri, adja meg a gyorsjavítási fájlokat tartalmazó megosztott hálózati mappa elérési útját.
5. A rendszer a művelet megerősítését kéri. A gyorsjavítás telepítésének folytatásához írja be az **Y karaktert** .

