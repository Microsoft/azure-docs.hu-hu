---
title: Microsoft Azure StorSimple Manager virtuális tömb felügyelete | Microsoft Docs
description: Ismerje meg, hogyan kezelheti a StorSimple helyszíni virtuális tömbjét a Azure Portal StorSimple Eszközkezelő szolgáltatásának használatával.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 958244a5-f9f5-455e-b7ef-71a65558872e
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/17/2021
ms.author: alkohli
ms.openlocfilehash: cb5b6b1a2d890fe49d78da17d549c5676557b971
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654245"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-virtual-array"></a>A StorSimple Eszközkezelő szolgáltatás használata a StorSimple virtuális tömb felügyeletéhez
![folyamat beállítása folyamatban](./media/storsimple-virtual-array-manager-service-administration/manage4.png)

## <a name="overview"></a>Áttekintés
Ez a cikk ismerteti a StorSimple Eszközkezelő szolgáltatás felületét, beleértve a kapcsolódást és a rendelkezésre álló különböző lehetőségeket, valamint a felhasználói felületen keresztül elvégezhető konkrét munkafolyamatokra mutató hivatkozásokat is tartalmaz.

A cikk elolvasása után a következőket fogja tudni:

* Kapcsolódás a StorSimple Eszközkezelő szolgáltatáshoz
* Navigáljon a StorSimple Eszközkezelő felhasználói felületén
* A StorSimple virtuális tömb felügyelete a StorSimple Eszközkezelő Service használatával

> [!NOTE]
> A StorSimple 8000 Series eszközhöz elérhető felügyeleti lehetőségek megtekintéséhez lépjen a [StorSimple Manager szolgáltatás használatára a StorSimple-eszköz felügyeletéhez](./storsimple-8000-manager-service-administration.md).

## <a name="connect-to-the-storsimple-device-manager-service"></a>Kapcsolódás a StorSimple Eszközkezelő szolgáltatáshoz
A StorSimple Eszközkezelő szolgáltatás Microsoft Azure fut, és több StorSimple virtuális tömbhöz csatlakozik. Az eszközök kezeléséhez egy böngészőben futó központi Microsoft Azure Portal fog használni. A StorSimple Eszközkezelő szolgáltatáshoz való kapcsolódáshoz tegye a következőket.

#### <a name="to-connect-to-the-service"></a>Kapcsolódás a szolgáltatáshoz
1. Ugrás a következőre: [https://portal.azure.com](https://portal.azure.com) .
2. A Microsoft-fiók hitelesítő adataival jelentkezzen be a Microsoft Azure Portalba (a panel jobb felső sarkában található).
3. Navigáljon a Tallózás gombra – > "filter" a StorSimple-Eszközkezelőben az összes Eszközkezelő egy adott előfizetésben való megtekintéséhez.

## <a name="use-the-storsimple-device-manager-service-to-perform-management-tasks"></a>A StorSimple Eszközkezelő szolgáltatás használata felügyeleti feladatok végrehajtásához
A következő táblázat a StorSimple Eszközkezelő szolgáltatás összefoglalási paneljén elvégezhető általános felügyeleti feladatok és összetett munkafolyamatok összegzését tartalmazza. Ezek a feladatok a kezdeményezett pengék alapján vannak rendszerezve.

Az egyes munkafolyamatokkal kapcsolatos további információkért kattintson a megfelelő eljárásra a táblázatban.

[!INCLUDE [StorSimple software warning](../../includes/storsimple-update-software-warning.md)]

#### <a name="storsimple-device-manager-workflows"></a>StorSimple Eszközkezelő munkafolyamatok
| Ha ezt szeretné tenni... | Az eljárás használata |
| --- | --- |
| Szolgáltatás létrehozása</br>Szolgáltatás törlése</br>Szolgáltatásregisztrációs kulcs lekérése</br>A szolgáltatás regisztrációs kulcsának újbóli előállítása |[A StorSimple Eszközkezelő szolgáltatás üzembe helyezése](storsimple-virtual-array-manage-service.md) |
| A tevékenység naplóinak megtekintése |[A StorSimple szolgáltatás összegzésének használata](storsimple-virtual-array-service-summary.md) |
| Virtuális tömb inaktiválása</br>Virtuális tömb törlése |[Virtuális tömb inaktiválása vagy törlése](storsimple-virtual-array-deactivate-and-delete-device.md) |
| Vész-helyreállítás és eszközök feladatátvétele</br>Feladatátvételi előfeltételek</br>Üzletmenet-folytonossági katasztrófa-helyreállítás (BCDR)</br>Hibák a vész-helyreállítás során |[Vész-helyreállítási és eszköz-feladatátvétel a StorSimple virtuális tömbhöz](storsimple-virtual-array-failover-dr.md) |
| Megosztások és kötetek biztonsági mentése</br>Manuális biztonsági mentés készítése</br>A biztonsági mentés ütemtervének módosítása</br>Meglévő biztonsági másolatok megtekintése |[A StorSimple virtuális tömb biztonsági mentése](storsimple-virtual-array-backup.md) |
| Megosztások klónozása biztonságimásolat-készletből</br>Kötetek klónozása biztonságimásolat-készletből</br>Elemszintű helyreállítás (csak fájlkiszolgáló) |[Klónozás a StorSimple virtuális tömb biztonsági másolatából](storsimple-virtual-array-clone.md) |
| Tudnivalók a Storage-fiókokról</br>Storage-fiók hozzáadása</br>Storage-fiók szerkesztése</br>Tárfiók törlése |[A StorSimple virtuális tömbhöz tartozó Storage-fiókok kezelése](storsimple-virtual-array-manage-storage-accounts.md) |
| Hozzáférés-vezérlési rekordok</br>Hozzáférés-vezérlési rekord hozzáadása vagy módosítása </br>Hozzáférés-vezérlési rekord törlése |[A StorSimple virtuális tömb hozzáférés-vezérlési rekordjainak kezelése](storsimple-virtual-array-manage-acrs.md) |
| Feladatok részleteinek megjelenítése |[StorSimple virtuális Array-feladatok kezelése](storsimple-virtual-array-manage-jobs.md) |
| Riasztási beállítások konfigurálása</br>Riasztási értesítések fogadása</br>Riasztások kezelése</br>Riasztások felülvizsgálata |[A StorSimple virtuális tömbhöz tartozó riasztások megtekintése és kezelése](storsimple-virtual-array-manage-alerts.md) |
| Az eszköz rendszergazdai jelszavának módosítása |[StorSimple virtuális tömb eszköz rendszergazdai jelszavának módosítása](storsimple-virtual-array-change-device-admin-password.md) |
| Szoftverfrissítések telepítése |[A virtuális tömb frissítése](storsimple-virtual-array-install-update.md) |

> [!NOTE]
> A [helyi webes felhasználói felületet](storsimple-ova-web-ui-admin.md) a következő feladatokhoz kell használnia:
> 
> * [A szolgáltatás adattitkosítási kulcsának beolvasása](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
> * [Támogatási csomag létrehozása](storsimple-ova-web-ui-admin.md#generate-a-log-package)
> * [Virtuális tömb leállítása és újraindítása](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)
> 
> 

## <a name="next-steps"></a>Következő lépések
A webes felhasználói felületről és annak használatáról a [StorSimple webes felhasználói felületének használata a StorSimple virtuális tömb felügyeletéhez](storsimple-ova-web-ui-admin.md)című témakörben olvashat bővebben.