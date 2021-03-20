---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 473bc0a58fe49c7f454c81402b57ddce7fc745b2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "67179180"
---
#### <a name="to-configure-remote-management-on-cloud-appliance"></a>A távfelügyelet konfigurálása a felhőalapú készüléken

1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Válassza ki a felhőalapú készüléket a szolgáltatáshoz csatlakoztatott eszközök listájából, majd kattintson rá.
    ![Felhőalapú készülék kiválasztása a StorSimple-ben](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage1.png)

2. Nyissa meg a **Beállítások > Biztonság** menüpont **Biztonsági beállítások** paneljét.

     ![A StorSimple biztonsági beállításai](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage2.png)

3. Lépjen a **Távfelügyelet** szakaszhoz. Kattintson a **Távfelügyelet** mezőre.
     ![StorSimple távfelügyelet](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage3.png)

4. A **Távfelügyelet** panelen:

    1. Győződjön meg arról, hogy a **Távfelügyelet engedélyezése** elem engedélyezve van.
    2. Az alapértelmezett csatlakozási mód a HTTPS protokoll használata. Igény szerint HTTP-n keresztül is csatlakozhat. A HTTP-n keresztüli csatlakozást kizárólag megbízható hálózatokon célszerű engedélyezni. Győződjön meg arról, hogy a HTTP lehetőség engedélyezve van.
    3. A panel tetején található parancssáv alatt kattintson a **... elemre. Továbbiak** , majd kattintson a **tanúsítvány letöltése** lehetőségre a távfelügyeleti tanúsítvány letöltéséhez. A fájl mentésének helyét is megadhatja. A tanúsítványt ezt követően telepítenie kell arra az ügyfélszámítógépre vagy gazdagépre, amellyel csatlakozni fog a felhőalapú készülékhez.

        ![Távfelügyelet panel](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage4.png)
5. Kattintson a **Mentés** parancsra, majd a rendszer kérdésének megjelenésekor hagyja jóvá a módosításokat.