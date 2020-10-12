---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: dc50f94ae9b207961a71480c2fc172e88db79cf4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "67179152"
---
#### <a name="to-install-regular-updates-via-windows-powershell-for-storsimple"></a>A rendszeres frissítések telepítése Windows PowerShell StorSimple-bővítménye használatával
1. Nyissa meg az eszköz soros konzolját, és válassza az 1. lehetőséget, **Jelentkezzen be teljes hozzáféréssel**. Írja be a jelszót. Az alapértelmezett jelszó a *jelszó1*. 
2. A parancssorba írja be a következőt:
   
     `Get-HcsUpdateAvailability`
   
    Értesítést kap, ha elérhetők a frissítések, és hogy a frissítések zavaróak vagy nem zavaróak-e.
3. A parancssorba írja be a következőt:
   
     `Start-HcsUpdate`
   
    A frissítési folyamat elindul.

> [!IMPORTANT]
> * Ez a parancs csak a rendszeres frissítésekre vonatkozik. Ezt a parancsot csak egy vezérlőn futtatja, de a rendszer mindkét vezérlőt frissíti. 
> * A frissítési folyamat során észreveheti, hogy a vezérlő feladatátvételt végez; a feladatátvétel azonban nem fogja érinteni a rendszer rendelkezésre állását vagy működését.
> 
> 

