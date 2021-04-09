---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9f1c30b303bd1fe02e0685c7d848be92073ca2f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96016869"
---
#### <a name="to-add-a-storage-account-credential-in-the-same-azure-subscription-as-the-storsimple-device-manager-service"></a>Tárfiók hitelesítő adatainak hozzáadása a StorSimple-eszközkezelővel meggyező Azure-előfizetésben

1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. A **Konfigurálás** szakaszban kattintson a **Tárfiók hitelesítő adatai** elemre.

    ![Nyissa meg a Storage-fiók hitelesítő adatait](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct1.png)

2. A **Tárfiók hitelesítő adatai** panelen kattintson a **+Hozzáadás** elemre.

    ![Tárfiók hitelesítő adatainak hozzáadása](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct2.png)

3. A **Tárfiók hitelesítő adatainak hozzáadása** panelen tegye a következőket:

    1. Ha ugyanahhoz az Azure-előfizetéshez szeretne tárfiók-hitelesítő adatokat hozzáadni, amelyhez a szolgáltatás is tartozik, győződjön meg arról, hogy az **Aktuális** elem van kiválasztva.

    2. Válasszon ki egy meglévő tárfiókot a **tárfiókok** legördülő listájából.

    3. A kiválasztott tárfiók alapján a rendszer megjeleníti a **helyet** (kiszürkítve jelenik meg, és itt nem módosítható).

    4. Válassza az **SSL-mód engedélyezése** lehetőséget, ha egy biztonságos csatornát szeretne létrehozni az eszköz és a felhő közötti hálózati kommunikációhoz. Csak akkor tiltsa le az **SSL engedélyezése** elemet, ha magánfelhőben tevékenykedik.

        ![Tárfiók-hitelesítő adatok hozzáadása panel](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct3.png)

    5. Kattintson a **Hozzáadás** elemre, amellyel elindítja a tárfiók-hitelesítő adatokhoz tartozó feladat létrehozását. Értesítést kap, ha a tárfiók-hitelesítő adatok sikeresen létrejöttek.

        ![Tárfiók-hitelesítő adatok – sikeres értesítés](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct5.png)

Az újonnan létrehozott tárfiók-hitelesítő adatok megjelennek a **Tárfiók hitelesítő adatai** listában.

![A Storage-fiók hitelesítő adatainak listázása](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct6.png)

