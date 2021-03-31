---
title: Az 5,1-es frissítés telepítése a StorSimple 8000 Series eszközön | Microsoft Docs
description: Ismerteti, hogyan telepíthető a StorSimple 8000 Series Update 5,1 a StorSimple 8000 Series eszközre.
services: storsimple
documentationcenter: NA
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/05/2020
ms.author: alkohli
ms.openlocfilehash: 5b9958f3dd497aa612a92947b8d968439ef9d0e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91575962"
---
# <a name="install-update-51-on-your-storsimple-device"></a>Az 5,1-es frissítés telepítése a StorSimple-eszközön

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag azt ismerteti, hogyan telepítheti a 5,1-es frissítést egy korábbi verziót futtató StorSimple-eszközön a Azure Portal használatával. <!--The hotfix method is used when you are trying to install Update 5.1 on a device running pre-Update 3 versions. The hotfix method is also used when a gateway is configured on a network interface other than DATA 0 of the StorSimple device and you are trying to update from a pre-Update 1 software version.-->

Az 5,1-es frissítés magában foglalja a nem zavaró biztonsági frissítéseket. A nem zavaró vagy rendszeres frissítések a Azure Portal használatával alkalmazhatók <!--or by the hotfix method-->.

> [!IMPORTANT]
>
> * Az 5,1-es frissítés kötelezően frissül, és azonnal telepíteni kell. További információ: 5,1-es [kibocsátási megjegyzések frissítése](storsimple-update51-release-notes.md).
> * A telepítés előtt manuális és automatikus ellenőrzéseket végeznek az eszközök állapotának meghatározásához a hardver állapota és a hálózati kapcsolat alapján. Ezeket az előzetes ellenőrzéseket csak akkor hajtja végre, ha a Azure Portalről alkalmazza a frissítéseket.
> * Ha a gyorsjavítási módszer használatával szeretné telepíteni, forduljon a [Microsoft ügyfélszolgálatahoz](mailto:support@microsoft.com).

<!--
> * We strongly recommend that when updating a device running versions prior to Update 3, you install the updates using hotfix method. If you encounter any issues, [log a support ticket](storsimple-8000-contact-microsoft-support.md). 


> * We recommend that you install the software and other regular updates via the Azure portal. You should only go to the Windows PowerShell interface of the device (to install updates) if the pre-update gateway check fails in the portal. Depending upon the version you are updating from, the updates may take 4 hours (or greater) to install. The maintenance mode updates must be installed through the Windows PowerShell interface of the device. As maintenance mode updates are disruptive updates, these result in a down time for your device.


> * If running the optional StorSimple Snapshot Manager, ensure that you have upgraded your Snapshot Manager version to Update 5.1 prior to updating the device.
-->

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-51-through-the-azure-portal"></a>Telepítse a 5,1-es frissítést a Azure Portal

Az alábbi lépések végrehajtásával frissítheti az eszközt az 5,1-es [frissítéssel](storsimple-update51-release-notes.md).

> [!NOTE]
> A Microsoft további diagnosztikai adatokat kér le az eszközről. Ennek eredményeképpen, amikor az operatív csapatunk olyan eszközöket azonosít, amelyekkel problémák léptek fel, jobban felkészültünk az adatok gyűjtésére az eszközről és a problémák diagnosztizálására.

#### <a name="to-install-an-update-from-the-azure-portal"></a>Frissítés telepítése az Azure Portalról

1. A StorSimple szolgáltatás oldalán válassza ki az eszközét.

    ![Eszköz kiválasztása](./media/storsimple-8000-install-update-51/update1.png)

2. Navigáljon az **eszközbeállítások**  >  **eszköz frissítései** menüpontra.

    ![Képernyőkép a beállítások panelről, az eszköz frissítéseinek meghívása lehetőséggel.](./media/storsimple-8000-install-update-51/update2.png)

3. Megjelenik egy értesítés, ha új frissítések érhetők el. Azt is megteheti, hogy az **eszköz frissítései** panelen a **frissítések keresése** lehetőségre kattint. Létrejön egy feladat, amely megkeresi az elérhető frissítéseket. A feladat sikeres befejezéséről értesítést kap.

    ![Képernyőkép a beállítások panelről, ahol a rendszer meghívja a beállításokat, és az eszköz frissítései panel az új rendszeres frissítésekkel elérhető üzenet jelenik meg.](./media/storsimple-8000-install-update-51/update3.png)

4. Azt javasoljuk, hogy mielőtt alkalmazná a frissítést az eszközön, tekintse át a kibocsátási megjegyzéseket. A frissítések alkalmazásához kattintson a **frissítések telepítése** elemre. A frissítések alkalmazása előtt tekintse át az előfeltételeket a **rendszeres frissítések megerősítése** panelen. Jelölje be a jelölőnégyzetet annak jelzéséhez, hogy készen áll az eszköz frissítésére, majd kattintson a **telepítés** gombra.

    ![Képernyőkép az eszköz frissítései panelről, ahol a frissítések telepítése lehetőség ki van választva, valamint a rendszeres frissítések megerősítése az egyetértési lehetőséggel és a telepítési lehetőséggel.](./media/storsimple-8000-install-update-51/update4.png)

5. Megkezdődik az előfeltételek ellenőrzése. Ezekbe az ellenőrzésekbe a következők tartoznak:
   
   * **A vezérlő állapotának ellenőrzései** annak ellenőrzéséhez, hogy mindkét eszközvezérlő kifogástalan állapotú és online legyen.
   * **A hardverösszetevők állapotának ellenőrzései** annak ellenőrzéséhez, hogy a StorSimple eszközön lévő összes hardverösszetevő kifogástalan állapotú legyen.
   * **DATA 0 ellenőrzések** annak ellenőrzéséhez, hogy a DATA 0 engedélyezett legyen az eszközön. Ha ez az illesztőfelület nem engedélyezett, engedélyeznie kell, majd újra kell próbálkoznia.

     A frissítés letöltése és telepítése csak akkor történik meg, ha az összes ellenőrzés sikeresen befejeződött. Értesítést kap, amikor az ellenőrzések folyamatban vannak. Ha az előzetes ellenőrzések sikertelenek, akkor a hiba okai lesznek elérhetők. Oldja meg ezeket a problémákat, majd próbálja megismételni a műveletet. Elképzelhető, hogy kapcsolatba kell lépnie a Microsoft támogatási szolgálatával, ha nem tudja egyedül kezelni ezeket a problémákat.

7. Az előzetes ellenőrzések sikeres befejeződése után létrejön egy frissítési feladatok. A frissítési feladat sikeres létrehozásáról értesítést kap.
   
    ![Képernyőkép a következőről: "a szoftverfrissítési feladatok indítása".](./media/storsimple-8000-install-update-51/update6.png)
   
    A rendszer ezután alkalmazza a frissítést az eszközre.

9. A frissítés elvégzése néhány órát igényel. Válassza ki a frissítési feladatot, és kattintson a **Részletek** gombra, így bármikor megtekintheti a feladat részleteit.

    ![Képernyőkép az eszköz frissítései panelről, amelyen a szoftverfrissítések letöltése és telepítése folyamatban van, és a frissítések telepítése panel van kiválasztva.](./media/storsimple-8000-install-update-51/update8.png)

     Nyomon követheti a frissítési feladat előrehaladását az **eszközbeállítások > feladatokból** is. A **feladatok** panelen láthatja a frissítés folyamatát.

     ![Képernyőkép a beállítások panelről, ahol a feladatok felhívhatók, a feladatok panel pedig a frissítés előrehaladását mutatja.](./media/storsimple-8000-install-update-51/update7.png)

10. A feladatok befejezése után navigáljon az eszközbeállítások **> eszköz frissítései** elemre. A szoftver verzióját most frissíteni kell.


Ellenőrizze, hogy az eszközön fut-e a **StorSimple 8000 Series Update 5,1 (6.3.9600.17885)**. A **legutóbbi frissítés dátumát** módosítani kell.
<!-- 5.1 - KB 4542887-->

<!--You will now see that the Maintenance mode updates are available (this message might continue to be displayed for up to 24 hours after you install the updates). The steps to install maintenance mode update are detailed in the next section.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## Install Update 5.1 as a hotfix

The software versions that can be upgraded using the hotfix method are:

* Update 0.1, 0.2, 0.3
* Update 1, 1.1, 1.2
* Update 2, 2.1, 2.2
* Update 3, 3.1
* Update 4
* Update 5

> [!NOTE]
> The recommended method to install Update 5.1 is through the Azure portal when trying to update from Update 3 and later version. When updating a device running versions prior to Update 3, use this procedure. You can also use this procedure if you fail the gateway check when trying to install the updates through the Azure portal. The check fails when you have a gateway assigned to a non-DATA 0 network interface and your device is running a software version earlier than Update 1.

The hotfix method involves the following three steps:

1. Download the hotfixes from the Microsoft Update Catalog.
2. Install and verify the regular mode hotfixes.
3. Install and verify the maintenance mode hotfix.

#### Download updates for your device

You must download and install the following hotfixes in the prescribed order and the suggested folders:

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Software update<br> Download both _HcsSoftwareUpdate.exe_ and _CisMSDAgent.exe_ |Regular <br></br>Non-disruptive |~ 25 mins |FirstOrderUpdate|

If updating from a device running Update 4, you only need to install the OS cumulative updates as second order updates.

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |OS cumulative updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|

If installing from a device running Update 3 or earlier, install the following in addition to the cumulative updates.

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |LSI driver and firmware updates <br> USM firmware update (version 3.38) |Regular <br></br>Non-disruptive |~ 3 hrs <br> (includes 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |OS security updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |OS updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|



You may also need to install disk firmware updates on top of all the updates shown in the preceding tables. You can verify whether you need the disk firmware updates by running the `Get-HcsFirmwareVersion` cmdlet. If you are running these firmware versions: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N003`, `0107`, then you do not need to install these updates.

| Order | KB | Description | Update type | Install time | Install in folder|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Disk firmware |Maintenance <br></br>Disruptive |~ 30 mins | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * If updating from Update 4, the total install time is close to 4 hours.
> * Before using this procedure to apply the update, make sure that both the device controllers are online and all the hardware components are healthy.

Perform the following steps to download and install the hotfixes.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]
-->
<!--
[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]
-->

## <a name="next-steps"></a>Következő lépések

További információ az 5,1-es [frissítésről](storsimple-update51-release-notes.md).
