---
title: Azure-beli virtuális gépek biztonsági másolatainak kezelése és figyelése
description: Ismerje meg, hogyan kezelheti és figyelheti az Azure-beli virtuális gépek biztonsági mentéseit a Azure Backup szolgáltatás használatával.
ms.topic: conceptual
ms.date: 08/02/2020
ms.openlocfilehash: 51ce88bb67d64ce129a3479d38db9a66dfe65d0a
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635077"
---
# <a name="manage-azure-vm-backups-with-azure-backup-service"></a>Azure-beli virtuális gépek biztonsági másolatainak kezelése Azure Backup szolgáltatással

Ez a cikk azt ismerteti, hogyan kezelhetők a [Azure Backup szolgáltatással](backup-overview.md)biztonsági mentés alatt álló Azure-beli virtuális gépek (VM-EK). A cikk a tároló irányítópultján található biztonsági mentési információkat is összegzi.

A Azure Portal a Recovery Services-tároló irányítópultja hozzáférést biztosít a tár adataihoz, beleértve a következőket:

* A legújabb biztonsági mentés, amely egyben a legújabb visszaállítási pont is.
* A biztonsági mentési szabályzat.
* Az összes biztonsági mentési pillanatkép teljes mérete.
* A biztonsági mentésre engedélyezett virtuális gépek száma.

A biztonsági mentéseket az irányítópult használatával és az egyes virtuális gépekre történő lefúrással kezelheti. A gépek biztonsági mentésének megkezdéséhez nyissa meg a tárolót az irányítópulton.

![Teljes irányítópult nézet a csúszkával](./media/backup-azure-manage-vms/bottom-slider.png)

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="view-vms-on-the-dashboard"></a>Virtuális gépek megtekintése az irányítópulton

Virtuális gépek megtekintése a tároló irányítópultján:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. A bal oldali menüben válassza a **Minden szolgáltatás** lehetőséget.

    ![Válassza a Minden szolgáltatás elemet](./media/backup-azure-manage-vms/select-all-services.png)

1. A **Minden szolgáltatás** párbeszédpanelen írja be a következőt: *Recovery Services*. A rendszer a megadott kulcsszavak alapján szűri az erőforrások listáját. Az erőforrások listájából válassza a **Recovery Services-tárolók** elemet.

    ![Recovery Services-tárolók megadása és kiválasztása](./media/backup-azure-manage-vms/all-services.png)

    Megjelenik az előfizetésben található Recovery Services-tárolók listája.

1. A könnyű használat érdekében válassza a tár neve melletti rögzítés ikont, és válassza a **rögzítés az irányítópulton** lehetőséget.
1. Nyissa meg a tároló irányítópultját.

    ![Nyissa meg a tároló irányítópultját és a Beállítások panelt](./media/backup-azure-manage-vms/full-view-rs-vault.png)

1. A **biztonsági mentési elemek** csempén válassza az **Azure virtuális gép** lehetőséget.

    ![A biztonsági mentési elemek csempe megnyitása](./media/backup-azure-manage-vms/azure-virtual-machine.png)

1. A **biztonsági másolati elemek** ablaktáblán megtekintheti a védett virtuális gépek listáját. Ebben a példában a tároló egy virtuális gépet véd: *myVMR1*.  

    ![A biztonsági mentési elemek panel megtekintése](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

1. A tároló elem irányítópultján módosíthatja a biztonsági mentési házirendeket, futtathat egy igény szerinti biztonsági mentést, leállíthatja vagy folytathatja a virtuális gépek védelmét, törölheti a biztonsági másolati adatait, megtekintheti a visszaállítási pontokat, és futtathat egy visszaállítást.

    ![A biztonsági mentési elemek irányítópultja és a beállítások panel](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policy-for-a-vm"></a>Virtuális gép biztonsági mentési szabályzatának kezelése

### <a name="modify-backup-policy"></a>Biztonsági mentési szabályzat módosítása

Meglévő biztonsági mentési szabályzat módosítása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/). Nyissa meg a tároló irányítópultját.
2. A **> biztonsági mentési házirendek kezelése** lapon válassza ki az Azure-beli **virtuális gép** típusának biztonsági mentési szabályzatát.
3. Válassza a **módosítás** lehetőséget, és módosítsa a beállításokat.

### <a name="switch-backup-policy"></a>Biztonsági mentési szabályzat váltása

Biztonsági mentési szabályzat kezelése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/). Nyissa meg a tároló irányítópultját.
2. A **biztonsági mentési elemek** csempén válassza az **Azure virtuális gép** lehetőséget.

    ![A biztonsági mentési elemek csempe megnyitása](./media/backup-azure-manage-vms/azure-virtual-machine.png)

3. A **biztonsági másolati elemek** ablaktáblán megtekintheti a védett virtuális gépek listáját és a legutóbbi biztonsági mentési állapotot a legutóbbi visszaállítási pontok idejével.

    ![A biztonsági mentési elemek panel megtekintése](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

4. A tároló elem irányítópultján kiválaszthatja a biztonsági mentési szabályzatot.

   * A házirendek váltásához válasszon másik szabályzatot, majd kattintson a **Mentés** gombra. Ekkor a rendszer automatikusan alkalmazza az új házirendet a tárolón.

     ![Biztonsági mentési szabályzat kiválasztása](./media/backup-azure-manage-vms/backup-policy-create-new.png)

## <a name="run-an-on-demand-backup"></a>Igény szerinti biztonsági mentések futtatása

A virtuális gép igény szerinti biztonsági mentését a védelem beállítása után is futtathatja. Tartsa szem előtt az alábbi adatokat:

* Ha a kezdeti biztonsági mentés függőben van, az igény szerinti biztonsági mentés a virtuális gép teljes másolatát hozza létre a Recovery Services-tárolóban.
* Ha a kezdeti biztonsági mentés elkészült, az igény szerinti biztonsági mentés csak az előző pillanatképből származó módosításokat küldi el a Recovery Services-tárolónak. Ez a későbbi biztonsági másolatok mindig növekményes.
* Az igény szerinti biztonsági mentés megőrzési tartománya a biztonsági mentés indításakor megadott megőrzési érték.

> [!NOTE]
> A Azure Backup szolgáltatás naponta legfeljebb három igény szerinti biztonsági mentést támogat, valamint egy további ütemezett biztonsági mentést.

Igény szerinti biztonsági mentés elindítása:

1. A tároló [elem irányítópultján](#view-vms-on-the-dashboard), a **védett elem** alatt válassza a **biztonsági másolat elemet**.

    ![A biztonsági mentés most lehetőség](./media/backup-azure-manage-vms/backup-now-button.png)

2. A **biztonsági mentési felügyelet típusa** területen válassza az Azure-beli **virtuális gép** lehetőséget. Megjelenik a **biztonsági mentési elem (Azure-beli virtuális gép)** panel.
3. Válasszon ki egy virtuális gépet, és válassza a **biztonsági mentés** lehetőséget egy igény szerinti biztonsági mentés létrehozásához. Megjelenik a **biztonsági mentés** panel.
4. A **biztonsági mentés** és a megőrzés megtartása mezőben határozza meg a biztonsági mentés dátumát.

    ![A biztonsági mentés naptára](./media/backup-azure-manage-vms/backup-now-check.png)

5. A biztonsági mentési feladatok futtatásához kattintson **az OK gombra** .

A feladat előrehaladásának nyomon követéséhez a tároló irányítópultján válassza a **biztonsági mentési feladatok** csempét.

## <a name="stop-protecting-a-vm"></a>Virtuális gép védelmének leállítása

A virtuális gépek védelmét kétféleképpen állíthatja le:

* **A védelem leállítása és a biztonsági mentési adat megőrzése**. Ezzel a beállítással leállíthatja a virtuális gép védelmének jövőbeli biztonsági mentési feladatait. A Azure Backup szolgáltatás azonban megőrzi a biztonsági mentés alatt álló helyreállítási pontokat.  A helyreállítási pontok megőrzéséhez a tárolóban kell fizetnie (lásd a részletek [Azure Backup díjszabását](https://azure.microsoft.com/pricing/details/backup/) ). Szükség esetén visszaállíthatja a virtuális gépet. Ha úgy dönt, hogy folytatja a virtuális gépek védelmét, akkor a *biztonsági mentés folytatása* lehetőséget használhatja.
* **Állítsa le a védelmet, és törölje a biztonsági másolati fájlokat**. Ez a beállítás leállítja az összes jövőbeli biztonsági mentési feladatot a virtuális gép védelmére, és törli az összes helyreállítási pontot. Nem fogja tudni visszaállítani a virtuális gépet, és nem használhatja a *biztonsági mentés folytatása* lehetőséget.

>[!NOTE]
>Ha töröl egy adatforrást a biztonsági mentések leállítása nélkül, az új biztonsági mentések sikertelenek lesznek. A régi helyreállítási pontok a szabályzatnak megfelelően lejárnak, de a legutóbbi helyreállítási pont mindig a biztonsági másolatok leállítása és az adat törlése után marad.
>

### <a name="stop-protection-and-retain-backup-data"></a>A védelem leállítása és a biztonsági mentési adat megőrzése

A védelem leállítása és a virtuális gép adatának megőrzése:

1. A tár [elem irányítópultján](#view-vms-on-the-dashboard)válassza a **biztonsági mentés leállítása** lehetőséget.
2. Válassza a **biztonsági mentési adat megőrzése** lehetőséget, és szükség szerint erősítse meg a kijelölést. Ha kívánja, adjon hozzá egy megjegyzést. Ha nem tudja biztosan az elem nevét, vigye a kurzort a felkiáltójel fölé a név megtekintéséhez.

    ![Biztonsági mentési adat megőrzése](./media/backup-azure-manage-vms/retain-backup-data.png)

Egy értesítésből megtudhatja, hogy a biztonsági mentési feladatok le lettek állítva.

### <a name="stop-protection-and-delete-backup-data"></a>Védelem leállítása és biztonsági másolatok törlése

A védelem leállítása és a virtuális gép adattartalmának törlése:

1. A tár [elem irányítópultján](#view-vms-on-the-dashboard)válassza a **biztonsági mentés leállítása** lehetőséget.
2. Válassza a **biztonsági másolati elemek törlése** lehetőséget, és szükség szerint erősítse meg a kijelölést. Adja meg a biztonsági mentési elem nevét, és adja hozzá a kívánt megjegyzést.

    ![Biztonsági mentési adatok törlése](./media/backup-azure-manage-vms/delete-backup-data.png)

> [!NOTE]
> A törlési művelet elvégzése után a rendszer 14 napig őrzi [meg a biztonsági](./soft-delete-virtual-machines.md)másolatba mentett összes adat mentését. <br>Emellett [engedélyezheti vagy letilthatja a nem kötelező törlést](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete)is.

## <a name="resume-protection-of-a-vm"></a>Virtuális gép védelmének folytatása

Ha a virtuális gép védelme beállításnál a [védelem leállítása és a biztonsági mentési adat megőrzése](#stop-protection-and-retain-backup-data) lehetőséget választotta, akkor a **biztonsági mentés folytatása** lehetőséggel is használható. Ez a lehetőség nem érhető el, ha a [védelem leállítása és a biztonsági másolati beállítások törlése](#stop-protection-and-delete-backup-data) lehetőségre kattint, vagy [törli a biztonsági másolati fájlokat](#delete-backup-data).

A virtuális gép védelmének folytatása:

1. A tár [elem irányítópultján](#view-vms-on-the-dashboard)válassza a **biztonsági mentés folytatása** lehetőséget.

2. A virtuális gép szabályzatának hozzárendeléséhez kövesse a [biztonsági mentési házirendek kezelése](#manage-backup-policy-for-a-vm) című témakör lépéseit. Nem kell kiválasztania a virtuális gép kezdeti védelmi szabályzatát.
3. Miután alkalmazta a biztonsági mentési szabályzatot a virtuális gépre, a következő üzenet jelenik meg:

    ![A virtuális gép sikeres védelmét jelző üzenet](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Biztonsági mentési adatok törlése

A virtuális gép biztonsági mentési adatfájljait kétféleképpen törölheti:

* A tároló elem irányítópultján válassza a biztonsági mentés leállítása lehetőséget, és kövesse a [védelem leállítása és a biztonsági mentési adatok törlése](#stop-protection-and-delete-backup-data) beállítás utasításait.

  ![Válassza a biztonsági mentés leállítása lehetőséget.](./media/backup-azure-manage-vms/stop-backup-button.png)

* A tároló elem irányítópultján válassza a biztonsági másolatok adatainak törlése lehetőséget. Ez a beállítás akkor engedélyezett, ha a virtuális gép védelmének leállításakor a [védelem leállítását és a biztonsági mentési adat megőrzése](#stop-protection-and-retain-backup-data) beállítást választotta.

  ![Válassza a biztonsági mentés törlése lehetőséget.](./media/backup-azure-manage-vms/delete-backup-button.png)

  * A tároló [elem irányítópultján](#view-vms-on-the-dashboard)válassza a **biztonsági másolati elemek törlése** lehetőséget.
  * Írja be a biztonsági mentési tétel nevét annak megerősítéséhez, hogy törölni kívánja a helyreállítási pontokat.

    ![Biztonsági mentési adatok törlése](./media/backup-azure-manage-vms/delete-backup-data.png)

  * Az elemhez tartozó biztonsági másolati elemek törléséhez válassza a **Törlés** lehetőséget. Egy értesítési üzenetből megtudhatja, hogy a biztonsági mentési információ törölve lett.

Az adatai védelme érdekében Azure Backup tartalmazza a Soft delete funkciót. A helyreállítható törlés után még a virtuális gép biztonsági mentését (az összes helyreállítási pontot) is törli a rendszer, a biztonsági mentési adat 14 további napig megmarad. További információt [a Soft delete dokumentációjában](./backup-azure-security-feature-cloud.md)talál.

  > [!NOTE]
  > Ha törli a biztonsági mentési adatmennyiséget, az összes kapcsolódó helyreállítási pontot törli. A törlendő helyreállítási pontok nem választhatók ki.

### <a name="backup-item-where-primary-data-source-no-longer-exists"></a>Olyan biztonsági mentési elem, amelyben az elsődleges adatforrás már nem létezik

* Ha Azure Backuphoz konfigurált Azure-beli virtuális gépeket a védelem leállítása nélkül törlik vagy áthelyezik, akkor az ütemezett biztonsági mentési feladatok és az igény szerinti (ad-hoc) biztonsági mentési feladatok sikertelenek lesznek a hiba UserErrorVmNotFoundV2. A biztonsági mentés előzetes ellenőrzési művelete csak a sikertelen, igény szerinti biztonsági mentési feladatok esetében kritikusként jelenik meg (sikertelen ütemezett feladatok nem jelennek meg).
* Ezek a biztonsági mentési elemek aktívak maradnak a rendszernek a felhasználó által beállított biztonsági mentési és adatmegőrzési házirend betartásával. Az Azure-beli virtuális gépek biztonsági másolatait az adatmegőrzési szabályzatnak megfelelően megőrzi a rendszer. A lejárt helyreállítási pontok (a legutóbbi helyreállítási pont kivételével) a biztonsági mentési szabályzatban beállított megőrzési időtartam szerint törlődnek.
* A további díjak elkerülése érdekében ajánlott törölni azokat a biztonsági mentési elemeket, amelyekben az elsődleges adatforrás már nem létezik. Ez olyan forgatókönyv, ahol a törölt erőforrások biztonsági másolati elemének/adatának megkötése már nem szükséges, mivel a legutóbbi helyreállítási pont örökre megmarad, és a megfelelő biztonsági mentés díjszabása alapján számítjuk fel a díjat.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [készíthet biztonsági mentést az Azure-beli virtuális gépekről a virtuális gép beállításaiból](backup-azure-vms-first-look-arm.md).
* Megtudhatja, hogyan [állíthatja vissza a virtuális gépeket](backup-azure-arm-restore-vms.md).
* Ismerje meg, hogyan [figyelheti az Azure virtuális gépek biztonsági másolatait](./backup-azure-monitoring-built-in-monitor.md).
