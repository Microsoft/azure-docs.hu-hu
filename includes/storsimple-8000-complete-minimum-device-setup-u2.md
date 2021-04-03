---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 867cdc97ff91d5932230b733dee4d7660d499c39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96027650"
---
#### <a name="to-complete-the-minimum-storsimple-device-setup"></a>A minimális StorSimple eszközbeállítás befejezése

   > [!NOTE]
   > Az eszköz neve nem módosítható a minimális eszközbeállítás végrehajtása után.
   
1. Az **Eszközök** panelen szereplő eszközök táblázatos listájából válassza ki az eszközt, majd kattintson rá. Az eszköz állapota: **Beállításra kész**. Ekkor megnyílik az **Eszköz konfigurálása** panel.

     ![A StorSimple minimális eszközbeállításának hálózati adapterei](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig1.png)

2. Az **Eszköz konfigurálása** panelen:
   
   1. Adjon egy **rövid** nevet az eszköznek. Az alapértelmezett eszköznév olyan információkból áll, mint az eszköz típusa és sorozatszáma. Az eszköz kezelése érdekében egy legfeljebb 64 karakterből álló rövid nevet rendelhet hozzá.
   2. Az **időzóna** az eszköz üzembe helyezésének földrajzi helyétől függ. Az eszköz minden ütemezett művelethez ezt az időzónát használja.
   3. **A DATA 0 beállításai** között:

       1. A DATA 0 hálózati adapter a beállítási varázslóban megadott hálózati beállítások (IP-cím, alhálózat, átjáró) szerint jelenik meg. A DATA 0 a felhőben és az iSCSI-n is automatikusan engedélyezve van.

       2. Adja meg a Vezérlő 0 és a Vezérlő 1 fix IP-címét. **A vezérlő fix IP-címeinek az alhálózaton belüli, az eszköz IP-címe alapján elérhető, szabad IP-címeknek kell lenniük.** Ha a DATA 0 adapter az IPv4 használatára van konfigurálva, akkor a fix IP-címeket is IPv4-formátumban kell megadni. Ha IPv6-konfigurációhoz tartozó előtagot adott meg, a fix IP-címek automatikusan ezekbe a mezőkbe lesznek betöltve.

            ![StorSimple minimális eszköz beállítása hálózati adapterek 2](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig2.png)

            A vezérlőhöz tartozó fix IP-címek az eszköz frissítéseinek karbantartásához és szemétgyűjtéshez használatosak. Ezért a fix IP-knek irányíthatóknak kell lenniük, és csatlakoztatva kell lenniük az internethez. A fix vezérlő IP-k irányíthatóságát a [Test-HcsmConnection][Test] parancsmaggal ellenőrizheti. Az alábbi példán olyan, az internetre átirányított fix vezérlő IP-k láthatók, amelyek hozzáférnek a Microsoft Update-kiszolgálókhoz.

            ![Test-HcsmConnection irányítható IP-kkel](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig3.png)

1. Kattintson az **OK** gombra. Ekkor elkezdődik az eszköz konfigurálása. Az eszköz konfigurálásának befejezéséről a rendszer értesítést küld. Az eszköz **Elérhető** állapotúra vált az **Eszközök** panelen.

    ![StorSimple minimális eszköz beállítása hálózati adapterek 3](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig4.png)

<!--Link reference-->
[Test]: /previous-versions/windows/powershell-scripting/dn715782(v=wps.630)