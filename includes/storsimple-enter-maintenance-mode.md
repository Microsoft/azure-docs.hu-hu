---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d35f0ef783a2c48f8211657bc8829635c19495aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "67179146"
---
#### <a name="to-enter-maintenance-mode"></a>Karbantartási mód megadása
1. A soros konzol menüjében válassza az 1. lehetőséget, majd **Jelentkezzen be a teljes hozzáférés** lehetőséggel.
2. Írja be a jelszót. Az alapértelmezett jelszó a **jelszó1**.
3. A parancssorba írja be a következőt:
   
     `Enter-HcsMaintenanceMode`
4. Megjelenik egy figyelmeztető üzenet, amely tájékoztatja, hogy a karbantartási mód megszakítja az összes I/O-kérést, és a klasszikus Azure portálhoz való csatlakozást, és a rendszer megerősítést kér. A karbantartási mód megadásához írja be az **Y karaktert** .
   
    Mindkét vezérlő újra fog indulni. Ha az újraindítás befejeződött, egy másik üzenet jelenik meg, amely azt jelzi, hogy az eszköz karbantartási módban van.

