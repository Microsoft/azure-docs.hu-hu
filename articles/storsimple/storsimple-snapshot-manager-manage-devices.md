---
title: Eszközök kezelése a StorSimple Snapshot Managersal | Microsoft Docs
description: Ismerteti, hogyan használható a StorSimple Snapshot Manager MMC beépülő modul a StorSimple-eszközök csatlakoztatásához és kezeléséhez.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: 966ecbe3-a7fa-4752-825f-6694dd949946
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: dbb7e835d110bcb6cb81fd5425a2aafbb578e692
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96022972"
---
# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>A StorSimple Snapshot Manager használata a StorSimple-eszközök csatlakoztatásához és kezeléséhez
## <a name="overview"></a>Áttekintés
A StorSimple Snapshot Manager **hatóköre** ablaktáblán a csomópontok segítségével ellenőrizheti az importált StorSimple-eszközök adatai és a csatlakoztatott tárolóeszközök frissítése is. Emellett, ha az **eszközök** csomópontra kattint, a csatlakoztatott eszközök és a megfelelő állapotadatok listáját az **eredmények** ablaktábláján tekintheti meg.

![Csatlakoztatott eszközök](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**1. ábra: StorSimple Snapshot Manager csatlakoztatott eszköz** 

A **nézet** kiválasztásának függvényében az **eredmények** ablaktábla a következő információkat jeleníti meg az egyes eszközökről. (A nézetek konfigurálásával kapcsolatos további információkért nyissa meg a [Nézet menüt](storsimple-use-snapshot-manager.md#view-menu).

| Eredmények oszlop | Leírás |
|:--- |:--- |
| Név |A klasszikus Azure portálon konfigurált eszköz neve |
| Modellezés |Az eszköz modellje |
| Verzió |Az eszközre telepített szoftver verziója |
| Állapot |Az eszköz elérhető-e |
| Legutóbb szinkronizálva |Az eszköz legutóbbi szinkronizálásának dátuma és időpontja |
| Sorozatszám. |Az eszköz sorozatszáma |

Ha a jobb gombbal az **eszközök** csomópontra kattint a **hatókör** ablaktáblán, a következő műveletek közül választhat:

* Eszköz hozzáadása vagy cseréje
* Eszköz csatlakoztatása és az Importálások ellenőrzése
* Csatlakoztatott eszközök frissítése

Ha az **eszközök** csomópontra kattint, majd a jobb gombbal az eszköz nevére kattint, **az alábbi** műveletek közül választhat:

* Eszköz hitelesítése
* Eszközadatok megtekintése
* Eszköz frissítése
* Eszköz konfigurációjának törlése
* Eszköz jelszavának módosítása

> [!NOTE]
> Az összes művelet a **műveletek** ablaktáblán is elérhető.


Ez az oktatóanyag azt ismerteti, hogyan használható az StorSimple Snapshot Manager az eszközök csatlakoztatásához és kezeléséhez, valamint a következő feladatok elvégzéséhez:

* Eszköz hozzáadása vagy cseréje
* Eszköz csatlakoztatása és az Importálások ellenőrzése
* Csatlakoztatott eszközök frissítése
* Eszköz hitelesítése
* Eszközadatok megtekintése
* Egyéni eszköz frissítése
* Eszköz konfigurációjának törlése
* Lejárt eszköz jelszavának módosítása
* Sikertelen eszköz cseréje

> [!NOTE]
> Az StorSimple Snapshot Manager felületének használatával kapcsolatos általános információkért nyissa meg a [StorSimple Snapshot Manager felhasználói felületét](storsimple-use-snapshot-manager.md).


## <a name="add-or-replace-a-device"></a>Eszköz hozzáadása vagy cseréje
A következő eljárással adhat hozzá vagy cserélhet le egy StorSimple-eszközt.

#### <a name="to-add-or-replace-a-device"></a>Eszköz hozzáadása vagy cseréje
1. A StorSimple Snapshot Manager indításához kattintson az asztal ikonjára.
2. A **hatókör** ablaktáblán kattintson a jobb gombbal az **eszközök** csomópontra, majd kattintson az **eszköz konfigurálása** parancsra. Megjelenik az **eszköz konfigurálása** párbeszédpanel.
   
    ![StorSimple-eszköz konfigurálása](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 
3. Az **eszköz** legördülő listából válassza ki az eszköz vagy virtuális eszköz IP-címét. 
4. A **jelszó** szövegmezőbe írja be a klasszikus Azure portálon az eszközhöz létrehozott StorSimple Snapshot Manager jelszavát. Kattintson az **OK** gombra. A StorSimple Snapshot Manager megkeresi az Ön által azonosított eszközt. 
   
   * Ha az eszköz elérhető, a StorSimple Snapshot Manager hozzáadja a csatlakozást.
   * Ha az eszköz valamilyen okból nem érhető el, a StorSimple Snapshot Manager egy hibaüzenetet ad vissza. A hibaüzenet bezárásához kattintson az **OK** gombra, majd kattintson a **Mégse** gombra az **eszköz konfigurálása** párbeszédpanel bezárásához.

## <a name="connect-a-device-and-verify-imports"></a>Eszköz csatlakoztatása és az Importálások ellenőrzése
A következő eljárással csatlakozhat egy StorSimple-eszközhöz, és ellenőrizheti, hogy a társított biztonsági másolatokkal rendelkező meglévő kötetek importálása megtörtént-e.

#### <a name="to-connect-a-device-and-verify-imports"></a>Eszköz csatlakoztatása és az importálás ellenőrzése
1. Az eszköz StorSimple Snapshot Manager csatlakoztatásához kövesse az eszköz hozzáadása vagy cseréje című témakör utasításait. Ha egy eszközhöz csatlakozik, a StorSimple Snapshot Manager a következőképpen válaszol:
   
   * Ha az eszköz valamilyen okból nem érhető el, a StorSimple Snapshot Manager egy hibaüzenetet ad vissza. 
   
   * Ha az eszköz elérhető, a StorSimple Snapshot Manager hozzáadja a csatlakozást. Ha kijelöli az eszközt, az az **eredmények** ablaktáblájában jelenik meg, és az állapot mező jelzi, hogy az eszköz **elérhető**. A StorSimple Snapshot Manager importálja az eszközhöz konfigurált összes mennyiségi csoportot, ha a kötetek biztonsági másolatokkal rendelkeznek. A biztonsági mentési házirendek importálása nem történik meg. A társított biztonsági másolatokkal nem rendelkező kötetek importálása nem történik meg.
2. A StorSimple Snapshot Manager indításához kattintson az asztal ikonjára.
3. A **hatókör** ablaktáblán kattintson a jobb gombbal a legfelső csomópontra, majd kattintson az **importálások váltása megjelenítés** elemre.
   
    ![Az Importálások megjelenítésének kiválasztása](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 
4. Megjelenik az Importálások **megjelenítése** párbeszédpanel, amely az importált kötetek és biztonsági másolatok állapotát mutatja. Kattintson az **OK** gombra.

A kötetek és a biztonsági másolatok sikeres importálása után a StorSimple Snapshot Manager segítségével kezelheti őket, ugyanúgy, mint a StorSimple Snapshot Manager által létrehozott és konfigurált mennyiségi csoportok és biztonsági másolatok kezelése során. 

## <a name="refresh-connected-devices"></a>Csatlakoztatott eszközök frissítése
A következő eljárással szinkronizálhatja a csatlakoztatott StorSimple-eszközöket a StorSimple Snapshot Manager használatával.

#### <a name="to-refresh-connected-devices"></a>Csatlakoztatott eszközök frissítése
1. A StorSimple Snapshot Manager indításához kattintson az asztal ikonjára.
2. A **hatókör** ablaktáblán kattintson a jobb gombbal az **eszközök** elemre, majd kattintson az **eszközök frissítése** parancsra. Ezzel szinkronizálja a csatlakoztatott eszközöket a StorSimple Snapshot Manager, így megtekintheti a köteteket és a biztonsági másolatokat, beleértve a legújabb kiegészítéseket is. 
   
    ![A StorSimple-eszközök frissítése](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)

Az **eszközök frissítése** művelet lekérdezi a csatlakoztatott eszközökről az új kötet-és a kapcsolódó biztonsági másolatokat. A **kötetek** csomóponthoz elérhető **újraellenőrzési** művelettől eltérően a **frissítési eszközök** nem állítják vissza a biztonsági mentési beállításjegyzéket.

## <a name="authenticate-a-device"></a>Eszköz hitelesítése
Az alábbi eljárást követve hitelesítheti a StorSimple eszközt a StorSimple Snapshot Manager használatával.

#### <a name="to-authenticate-a-device"></a>Eszköz hitelesítése
1. A StorSimple Snapshot Manager indításához kattintson az asztal ikonjára.
2. A **hatókör** ablaktáblán kattintson az **eszközök** elemre.
3. Az **eredmények** ablaktáblájában kattintson a jobb gombbal az eszköz nevére, majd kattintson a **hitelesítés** elemre.
4. Megjelenik a **hitelesítés** párbeszédpanel. Írja be az eszköz jelszavát, majd kattintson **az OK** gombra.
   
    ![Hitelesítés párbeszédpanel](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 

## <a name="view-device-details"></a>Eszközadatok megtekintése
Az alábbi eljárással megtekintheti a StorSimple-eszközök részleteit, és szükség esetén újraszinkronizálhatja az eszközt a StorSimple Snapshot Manager.

#### <a name="to-view-and-resynchronize-device-details"></a>Az eszköz adatainak megtekintése és újraszinkronizálása
1. A StorSimple Snapshot Manager indításához kattintson az asztal ikonjára.
2. A **hatókör** ablaktáblán kattintson az **eszközök** elemre.
3. Az **eredmények** ablaktáblájában kattintson a jobb gombbal az eszköz nevére, majd kattintson a **részletek** elemre.

4. megjelenik az **eszköz részletei** párbeszédpanel. Ebben a mezőben a név, a modell, a verziószám, a sorozatszám, az állapot, a célként megadott iSCSI minősített neve (IQN) és a legutóbbi szinkronizálás dátuma és időpontja látható.

* Az eszköz szinkronizálásához kattintson az **Újraszinkronizálás** elemre.
* A párbeszédpanel bezárásához kattintson **az OK** vagy a **Mégse** gombra.
  
  ![Eszközadatok](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 

## <a name="refresh-an-individual-device"></a>Egyéni eszköz frissítése
A következő eljárással újraszinkronizálhatja az egyes StorSimple-eszközöket a StorSimple Snapshot Manager használatával.

#### <a name="to-refresh-a-device"></a>Eszköz frissítése
1. A StorSimple Snapshot Manager indításához kattintson az asztal ikonjára. 
2. A **hatókör** ablaktáblán kattintson az **eszközök** elemre. 
3. Az **eredmények** ablaktáblájában kattintson a jobb gombbal az eszköz nevére, majd kattintson az **eszköz frissítése** parancsra. Ezzel szinkronizálja az eszközt a StorSimple Snapshot Manager.

## <a name="delete-a-device-configuration"></a>Eszköz konfigurációjának törlése
A következő eljárással törölheti az egyes StorSimple-eszközök konfigurációját a StorSimple Snapshot Managerból.

#### <a name="to-delete-a-device-configuration"></a>Eszköz konfigurációjának törlése
1. A StorSimple Snapshot Manager indításához kattintson az asztal ikonjára.
2. A **hatókör** ablaktáblán kattintson az **eszközök** elemre. 
3. Az **eredmények** ablaktáblájában kattintson a jobb gombbal az eszköz nevére, majd kattintson a **Törlés** parancsra. 
4. A következő üzenet jelenik meg. Kattintson az **Igen** gombra a konfiguráció törléséhez, vagy kattintson a **nem** gombra a törlés megszakításához.
   
    ![Eszköz konfigurációjának törlése](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Lejárt eszköz jelszavának módosítása
Meg kell adnia egy jelszót a StorSimple-eszköz StorSimple Snapshot Manager való hitelesítéséhez. Ezt a jelszót akkor konfigurálja, ha a Windows PowerShell felületét használja az eszköz beállításához. A jelszó azonban lejárhat. Ha ez történik, a klasszikus Azure portál használatával módosíthatja a jelszót. Ezt követően, mivel az eszköz StorSimple Snapshot Manager lett konfigurálva a jelszó lejárta előtt, újra hitelesítenie kell az eszközt a StorSimple-Snapshot Managerban.

#### <a name="to-change-the-expired-password"></a>A lejárt jelszó módosítása
1. A klasszikus Azure portálon indítsa el a StorSimple Manager szolgáltatást.
2. Kattintson az **eszközök**  >  **konfigurálása** az eszközhöz elemre.
3. Görgessen le a StorSimple Snapshot Manager szakaszhoz. 14-15 karakterből álló jelszót adjon meg. Győződjön meg arról, hogy a jelszó kis-és nagybetűket, számokat és speciális karaktereket tartalmaz.
4. Írja be újra a jelszót a megerősítéshez.
5. Kattintson a lap alján található **Mentés** gombra.

#### <a name="to-re-authenticate-the-device"></a>Az eszköz újbóli hitelesítése
1. Indítsa el a StorSimple Snapshot Manager.
2. A **hatókör** ablaktáblán kattintson az **eszközök** elemre. A konfigurált eszközök listája megjelenik az **eredmények** ablaktábláján.
3. Válassza ki az eszközt, kattintson a jobb gombbal, majd kattintson a **hitelesítés** elemre.
4. A **hitelesítés** ablakban adja meg az új jelszót.
5. Válassza ki az eszközt, kattintson a jobb gombbal, majd válassza az **eszköz frissítése** lehetőséget. Ezzel szinkronizálja az eszközt a StorSimple Snapshot Manager.

## <a name="replace-a-failed-device"></a>Sikertelen eszköz cseréje
Ha egy StorSimple-eszköz meghibásodik, és egy készenléti (feladatátvételi) eszköz váltja fel, akkor a következő lépésekkel csatlakozhat az új eszközhöz, és megtekintheti a kapcsolódó biztonsági másolatokat.

#### <a name="to-connect-to-a-new-device-after-failover"></a>Kapcsolódás új eszközhöz a feladatátvétel után
1. Konfigurálja újra az iSCSI-kapcsolatokat az új eszközzel. Útmutatásért keresse fel a helyszíni [StorSimple-eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md)című témakör 7. lépés: kötet csatlakoztatása, inicializálása és formázása című szakaszát.

> [!NOTE]
> Ha az új StorSimple-eszköz ugyanazzal az IP-címmel rendelkezik, mint a régi, akkor lehet, hogy csatlakozni tud a régi konfigurációhoz.


1. Állítsa le a Microsoft StorSimple-kezelő szolgáltatást:
   
   1. Indítsa el a Kiszolgálókezelőt.
   2. A Kiszolgálókezelő irányítópult **eszközök** menüjében válassza a **szolgáltatások** lehetőséget.
   3. A **szolgáltatások** ablakban válassza ki a **Microsoft StorSimple-kezelő szolgáltatást**.
   4. A jobb oldali ablaktábla **Microsoft StorSimple-kezelő szolgáltatása** területén kattintson **a szolgáltatás leállítása** elemre.
2. Távolítsa el a régi eszközhöz kapcsolódó konfigurációs adatokat:
   
   1. A Fájlkezelőben keresse meg a C:\ProgramData\Microsoft\StorSimple\BACatalog.
   2. Törölje a fájlokat a BACatalog mappában.
3. Indítsa újra a Microsoft StorSimple-kezelő szolgáltatást:
   
   1. A Kiszolgálókezelő irányítópult **eszközök** menüjében válassza a **szolgáltatások** lehetőséget.
   2. A **szolgáltatások** ablakban válassza ki a **Microsoft StorSimple-kezelő szolgáltatást**.
   3. A jobb oldali ablaktábla **Microsoft StorSimple-kezelő szolgáltatása** területén kattintson **a szolgáltatás újraindítása** elemre.
4. Indítsa el a StorSimple Snapshot Manager.
5. Az új StorSimple-eszköz konfigurálásához végezze el a 2. lépés: StorSimple-eszköz csatlakoztatása a [StorSimple üzembe helyezése Snapshot Manager](storsimple-snapshot-manager-deployment.md)című szakasz lépéseit.
6. A **hatókör** ablaktáblán kattintson a jobb gombbal a legfelső szintű csomópontra (a példában a StorSimple Snapshot Manager), majd kattintson az **importálások megjelenítése** elemre. 
7. Üzenet jelenik meg, amikor az importált kötetek és a biztonsági másolatok láthatók a StorSimple Snapshot Manager. Kattintson az **OK** gombra.

## <a name="next-steps"></a>Következő lépések
* Ismerje meg, hogyan kezelheti [a StorSimple-megoldást a StorSimple Snapshot Manager használatával](storsimple-snapshot-manager-admin.md).
* Megtudhatja, hogyan [használhatja a StorSimple Snapshot Manager a kötetek megtekintésére és kezelésére](storsimple-snapshot-manager-manage-volumes.md).

