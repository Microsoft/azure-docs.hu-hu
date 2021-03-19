---
title: StorSimple Eszközkezelő Service Administration | Microsoft Docs
description: Ismerje meg, hogyan kezelheti a StorSimple-eszközt a Azure Portal StorSimple Eszközkezelő szolgáltatásának használatával.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/17/2021
ms.author: alkohli
ms.openlocfilehash: 0e25d41fbf6a9683f178cbfd1b07fde1b6796509
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656821"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-device"></a>A StorSimple-eszköz felügyeletéhez használja a StorSimple Eszközkezelő szolgáltatást

## <a name="overview"></a>Áttekintés

Ez a cikk ismerteti a StorSimple Eszközkezelő szolgáltatás felületét, beleértve a csatlakozást, a különböző elérhető lehetőségeket, valamint a felhasználói felületen keresztül elvégezhető konkrét munkafolyamatokra mutató hivatkozásokat. Ez az útmutató mindkettőre vonatkozik; a StorSimple fizikai eszköz és a felhőalapú berendezés.

A cikk elolvasása után a következőket fogja megtanulni:

* Kapcsolódás a StorSimple Eszközkezelő szolgáltatáshoz
* A StorSimple-eszköz felügyelete a StorSimple Eszközkezelő szolgáltatáson keresztül

## <a name="connect-to-storsimple-device-manager-service"></a>Kapcsolódás a StorSimple Eszközkezelő szolgáltatáshoz

A StorSimple Eszközkezelő szolgáltatás Microsoft Azure fut, és több StorSimple-eszközhöz csatlakozik. Az eszközök kezeléséhez egy böngészőben futó központi Microsoft Azure Portal fog használni. A StorSimple Eszközkezelő szolgáltatáshoz való kapcsolódáshoz tegye a következőket.

#### <a name="to-connect-to-the-service"></a>Kapcsolódás a szolgáltatáshoz
1. Navigáljon a következőhöz: [https://portal.azure.com/](https://portal.azure.com/) .
2. A Microsoft-fiók hitelesítő adataival jelentkezzen be a Microsoft Azure Portalba (a panel jobb felső sarkában található).
3. Görgessen le a bal oldali navigációs ablaktáblán a StorSimple Eszközkezelő szolgáltatás eléréséhez.


## <a name="administer-storsimple-device-using-storsimple-device-manager-service"></a>StorSimple-eszköz felügyelete a StorSimple Eszközkezelő szolgáltatással

A következő táblázat a StorSimple Eszközkezelő szolgáltatás felhasználói felületén elvégezhető általános felügyeleti feladatok és összetett munkafolyamatok összegzését tartalmazza. Ezek a feladatok a kezdeményezett felhasználói felületi lapok alapján vannak rendszerezve.

Az egyes munkafolyamatokkal kapcsolatos további információkért kattintson a megfelelő eljárásra a táblázatban.

[!INCLUDE [StorSimple software warning](../../includes/storsimple-update-software-warning.md)]

#### <a name="storsimple-device-manager-workflows"></a>StorSimple Eszközkezelő munkafolyamatok

| Ha ezt szeretné tenni... | Használja ezt az eljárást. |
| --- | --- |
| Szolgáltatás létrehozása</br>Szolgáltatás törlése</br>Szolgáltatás regisztrációs kulcsának beolvasása</br>Szolgáltatás regisztrációs kulcsának újrakészítése |[StorSimple Eszközkezelő szolgáltatás üzembe helyezése](storsimple-8000-manage-service.md) |
| A tevékenység naplóinak megtekintése |[A StorSimple Eszközkezelő szolgáltatás összegzésének használata](storsimple-8000-service-dashboard.md) |
| A szolgáltatásban tárolt adattitkosítási kulcs módosítása</br>A műveleti naplók megtekintése |[A StorSimple Eszközkezelő szolgáltatás irányítópultjának használata](storsimple-8000-service-dashboard.md) |
| Eszköz inaktiválása</br>Eszköz törlése |[Eszköz inaktiválása vagy törlése](storsimple-8000-deactivate-and-delete-device.md) |
| Tudnivalók a vész-helyreállításról és az eszközök feladatátvételéről</br>Feladatátvétel fizikai eszközre</br>Feladatátvétel virtuális eszközre</br>Üzletmenet-folytonossági katasztrófa-helyreállítás (BCDR) |[Feladatátvétel és vész-helyreállítás a StorSimple-eszközön](storsimple-8000-device-failover-disaster-recovery.md) |
| Kötet biztonsági másolatainak listázása</br>Biztonságimásolat-készlet kiválasztása</br>Biztonságimásolat-készlet törlése |[Biztonsági másolatok kezelése](storsimple-8000-manage-backup-catalog.md) |
| Kötet klónozása |[Kötet klónozása](storsimple-8000-clone-volume-u2.md) |
| Biztonságimásolat-készlet visszaállítása |[Biztonságimásolat-készlet visszaállítása](storsimple-8000-restore-from-backup-set-u2.md) |
| Tudnivalók a Storage-fiókokról</br>Storage-fiók hozzáadása</br>Storage-fiók szerkesztése</br>Tárfiók törlése</br>Storage-fiókok kulcsának rotációja |[Tárfiókok kezelése](storsimple-8000-manage-storage-accounts.md) |
| Tudnivalók a sávszélesség-sablonokról</br>Sávszélesség-sablon hozzáadása</br>Sávszélesség-sablon szerkesztése</br>Sávszélesség-sablon törlése</br>Alapértelmezett sávszélesség-sablon használata</br>Egy teljes napi sávszélesség-sablon létrehozása, amely egy adott időpontban indul |[Sávszélességsablonok kezelése](storsimple-8000-manage-bandwidth-templates.md) |
| Hozzáférés-vezérlési rekordok</br>Hozzáférés-vezérlési rekord létrehozása</br>Hozzáférés-vezérlési rekord szerkesztése</br>Hozzáférés-vezérlési rekord törlése |[Hozzáférés-vezérlési rekordok kezelése](storsimple-8000-manage-acrs.md) |
| Feladatok részleteinek megjelenítése</br>Feladat megszakítása |[Feladatok kezelése](storsimple-8000-manage-jobs-u2.md) |
| Riasztási értesítések fogadása</br>Riasztások kezelése</br>Riasztások felülvizsgálata |[StorSimple-riasztások megtekintése és kezelése](storsimple-8000-manage-alerts.md) |
| Figyelési diagramok létrehozása |[A StorSimple-eszköz figyelése](./storsimple-8000-monitor-device.md) |
| Mennyiségi tároló hozzáadása</br>Mennyiségi tároló módosítása</br>Mennyiségi tároló törlése |[Kötettárolók kezelése](storsimple-8000-manage-volume-containers.md) |
| Kötet hozzáadása</br>Kötet módosítása</br>Kötet offline állapotba helyezése</br>Kötet törlése</br>Kötet figyelése |[Kötetek kezelése](storsimple-8000-manage-volumes-u2.md) |
| Eszközbeállítások módosítása</br>Időbeli beállítások módosítása</br>DNS.md beállításainak módosítása</br>Hálózati adapterek konfigurálása |[Eszköz konfigurációjának módosítása a StorSimple-eszközhöz](storsimple-8000-modify-device-config.md) |
| Webproxy beállításainak megtekintése |[Webproxy konfigurálása az eszközhöz](storsimple-8000-configure-web-proxy.md) |
| Az eszköz rendszergazdai jelszavának módosítása</br>StorSimple Snapshot Manager jelszavának módosítása |[StorSimple jelszavának módosítása](storsimple-8000-change-passwords.md) |
| Távfelügyelet konfigurálása |[Távoli kapcsolódás a StorSimple-eszközhöz](storsimple-8000-remote-connect.md) |
| Riasztási beállítások konfigurálása |[StorSimple-riasztások megtekintése és kezelése](storsimple-8000-manage-alerts.md) |
| A CHAP konfigurálása a StorSimple-eszközhöz |[A CHAP konfigurálása a StorSimple-eszközhöz](./storsimple-8000-configure-chap.md) |
| Biztonsági mentési szabályzat hozzáadása</br>Ütemterv hozzáadása vagy módosítása</br>Biztonsági mentési szabályzat törlése</br>Manuális biztonsági mentés készítése</br>Egyéni biztonsági mentési szabályzat létrehozása több kötettel és ütemtervtel |[Biztonsági mentési szabályzatok kezelése](storsimple-8000-manage-backup-policies-u2.md) |
| Az eszközök vezérlőinek leállítása</br>Eszközillesztők újraindítása</br>Vezérlők leállítása</br>Az eszköz visszaállítása a gyári beállításokra</br>(Csak a helyszíni eszközök esetében) |[StorSimple-eszköz vezérlő kezelése](storsimple-8000-manage-device-controller.md) |
| A StorSimple hardveres összetevőinek megismerése</br>Hardverállapot monitorozása</br>(Csak a helyszíni eszközök esetében) |[Hardver-összetevők figyelése](storsimple-8000-monitor-hardware-status.md) |
| Támogatási csomag létrehozása |[Támogatási csomag létrehozása és kezelése](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple) |
| Szoftverfrissítések telepítése |[Az eszköz frissítése](storsimple-update-device.md) |

## <a name="next-steps"></a>Következő lépések

Ha bármilyen problémát tapasztal a StorSimple-eszköz napi működésével vagy annak hardveres összetevőivel kapcsolatban, tekintse meg a következőt:

* [Hibakeresés a diagnosztikai eszköz használatával](storsimple-8000-diagnostics.md)
* [StorSimple-figyelési kijelző LED-ek használata](storsimple-monitoring-indicators.md)

Ha nem tudja elhárítani a problémákat, és létre kell hoznia egy szolgáltatási kérelmet, tekintse meg a [kapcsolatfelvételi Microsoft ügyfélszolgálata](storsimple-8000-contact-microsoft-support.md).