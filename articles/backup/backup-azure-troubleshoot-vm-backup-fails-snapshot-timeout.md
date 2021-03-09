---
title: Az ügynök és a bővítmény hibáinak elhárítása
description: Az ügynökkel, bővítménnyel és lemezekkel kapcsolatos Azure Backup hibák tünetei, okai és megoldása.
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.service: backup
ms.openlocfilehash: 0313394ad149460f82c98c63cab95b922b4a3da2
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519605"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Azure Backup hibával kapcsolatos hibák elhárítása: az ügynökkel vagy bővítménnyel kapcsolatos problémák

Ez a cikk hibaelhárítási lépéseket tartalmaz, amelyek segítségével megoldhatja a virtuálisgép-ügynökkel és-bővítménysel folytatott kommunikációhoz kapcsolódó Azure Backup hibákat.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="step-by-step-guide-to-troubleshoot-backup-failures"></a>Részletes útmutató a biztonsági mentési hibák elhárításához

A leggyakoribb biztonsági mentési hibák a következő hibaelhárítási lépések követésével önállóan oldhatók fel:

### <a name="step-1-check-azure-vm-health"></a>1. lépés: az Azure-beli virtuális gép állapotának keresése

- Győződjön meg arról, hogy az Azure-beli **virtuális gép kiépítési állapota "fut"**: Ha a [virtuális gép kiépítési állapota](../virtual-machines/states-billing.md) **leállított/nem lefoglalt/frissítési** állapotban van, akkor az nem fog tudni a biztonsági mentési művelettel. Nyissa meg *Azure Portal > virtuálisgép-> áttekintés >* , és ellenőrizze a virtuális gép állapotát, és győződjön meg róla, hogy **fut**  , és próbálkozzon újra a biztonsági mentési művelettel
- **Tekintse át a függőben lévő operációs rendszer frissítéseit vagy újraindítását**: Győződjön meg arról, hogy nincs függőben lévő operációsrendszer-frissítés vagy függőben lévő újraindítás a virtuális gépen.

### <a name="step-2-check-azure-vm-guest-agent-service-health"></a>2. lépés: az Azure VM Guest Agent szolgáltatás állapotának keresése

- **Győződjön meg arról, hogy az Azure VM Guest Agent szolgáltatás elindult és** naprakész:
  - Windows rendszerű virtuális gépen:
    - Navigáljon a **Services. msc szolgáltatáshoz** , és győződjön meg arról, hogy a **Windows Azure VM Guest Agent szolgáltatás** működik. Győződjön meg arról is, hogy a [legújabb verzió](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) van telepítve. További információ: a [Windows virtuális gép vendég ügynökével kapcsolatos problémák](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms).
    - Az Azure-beli virtuálisgép-ügynök alapértelmezés szerint a portál, a PowerShell, a parancssori felület vagy egy Azure Resource Manager sablon egyik Azure Piactéri rendszerképből üzembe helyezett Windows rendszerű virtuális gépen települ. [Az ügynök manuális telepítése](../virtual-machines/extensions/agent-windows.md#manual-installation) akkor lehet szükséges, ha az Azure-ban üzembe helyezett egyéni virtuálisgép-lemezképet hoz létre.
    - Tekintse át a támogatási mátrixot annak ellenőrzéséhez, hogy a virtuális gép fut-e a [támogatott Windows operációs rendszeren](backup-support-matrix-iaas.md#operating-system-support-windows).
  - Linux rendszerű virtuális gépen
    - A parancs végrehajtásával győződjön meg arról, hogy az Azure VM Guest Agent szolgáltatás fut `ps-e` . Győződjön meg arról is, hogy a [legújabb verzió](../virtual-machines/extensions/update-linux-agent.md) van telepítve. További információ: Linux rendszerű [virtuális gép vendég ügynökével kapcsolatos problémák](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms).
    - Győződjön meg arról, hogy a [Linux rendszerű virtuális gépek ügynökének függőségei a rendszercsomagokban](../virtual-machines/extensions/agent-linux.md#requirements) a támogatott konfigurációval rendelkeznek. Például: a Python 2,6-es és újabb verziója támogatott.
    - Tekintse át a támogatási mátrixot annak ellenőrzéséhez, hogy a virtuális gép fut-e a [támogatott Linux operációs rendszeren.](backup-support-matrix-iaas.md#operating-system-support-linux)

### <a name="step-3-check-azure-vm-extension-health"></a>3. lépés: az Azure VM-bővítmény állapotának megtekintése

- Győződjön meg arról, hogy az összes Azure-beli virtuálisgép **-bővítmény "kiépítés sikeres" állapotban** van: Ha bármely bővítmény hibás állapotban van, akkor az zavarhatja a biztonsági mentést.
- *Nyissa meg Azure Portal > virtuálisgép-> beállításait > bővítmények > bővítmények állapota* elemre, és ellenőrizze, hogy a bővítmények **kiépítés sikeres** állapotban van-e.
- Győződjön meg arról, hogy az összes [bővítményi probléma](../virtual-machines/extensions/overview.md#troubleshoot-extensions) megoldódott, és próbálkozzon újra a biztonsági mentési művelettel.
- Győződjön meg arról, hogy a **com+ rendszeralkalmazás** működik. Emellett a **Distributed Transaction coordinator szolgáltatásnak** **hálózati szolgáltatás fiókként** kell futnia. A [com+ és az MSDTC hibáinak elhárításához](backup-azure-vms-troubleshoot.md#extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error)kövesse a cikkben ismertetett lépéseket.

### <a name="step-4-check-azure-backup-vm-extension-health"></a>4. lépés: Azure Backup virtuálisgép-bővítmény állapotának keresése

A Azure Backup a virtuálisgép-Pillanatképek bővítmény használatával készíti el az Azure-beli virtuális gép alkalmazásának konzisztens biztonsági mentését. Azure Backup a biztonsági mentés engedélyezése után telepíti a bővítményt az első ütemezett biztonsági mentés részeként.

- Győződjön meg arról, hogy a **VMSnapshot-bővítmény nem meghibásodott állapotban** van: kövesse az ebben a [szakaszban](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state) felsorolt lépéseket az Azure Backup-bővítmény kifogástalan állapotának ellenőrzéséhez és biztosításához.

- **Ellenőrizze, hogy a víruskereső blokkolja-e a bővítményt**: bizonyos víruskereső szoftverek megakadályozhatják a bővítmények végrehajtását.
  
  A biztonsági mentési hiba időpontjában ellenőrizze, hogy vannak-e naplóbejegyzések a ***Eseménynapló Application logs** _ *_Hibás alkalmazás neve: IaaSBcdrExtension.exe_* *. Ha a bejegyzéseket látja, akkor lehet, hogy a virtuális gépen konfigurált víruskereső korlátozza a biztonsági mentési bővítmény végrehajtását. A teszteléshez zárja ki a következő könyvtárakat a víruskereső-konfigurációban, majd próbálja megismételni a biztonsági mentési műveletet.
  - `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`
  - `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`

- Ellenőrizze, hogy szükséges-e a **hálózati hozzáférés**: a bővítmények letöltése az Azure Storage bővítmény adattárában és a bővítmény állapotának feltöltése az Azure Storage-ba történik. [További információk](../virtual-machines/extensions/features-windows.md#network-access).
  - Ha az ügynök nem támogatott verzióját használja, engedélyeznie kell a kimenő hozzáférést az adott régióban lévő Azure Storage-hoz a virtuális gépről.
  - Ha letiltotta a hozzáférést `168.63.129.16` a vendég tűzfal vagy egy proxy használatával, a bővítmények a fentiektől függetlenül sikertelenek lesznek. A 80, 443 és 32526 portok szükségesek, [További információ](../virtual-machines/extensions/features-windows.md#network-access).

- Győződjön meg arról, hogy a **DHCP engedélyezve van a vendég virtuális gépen**: Ez azért szükséges, hogy a IaaS virtuális gép biztonsági mentése a DHCP-ből beolvassa a gazdagép vagy a háló címeit. Ha statikus magánhálózati IP-címmel kell rendelkeznie, konfigurálja azt a Azure Portal vagy a PowerShell használatával, és győződjön meg arról, hogy a virtuális gépen belül a DHCP-beállítás engedélyezve van, [További információ](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken).

- **Ellenőrizze, hogy a VSS-író szolgáltatás működik-** e: a VSS- [író hibáinak elhárításához](backup-azure-vms-troubleshoot.md#extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state)kövesse az alábbi lépéseket.
- **Kövesse a biztonsági mentéssel kapcsolatos ajánlott** eljárásokat ismertető útmutatót: az [Azure virtuális gépek biztonsági mentésének engedélyezésére vonatkozó ajánlott eljárások](backup-azure-vms-introduction.md#best-practices)áttekintése.
- A **titkosított lemezekre vonatkozó irányelvek áttekintése**: Ha engedélyezi a titkosított lemezekkel rendelkező virtuális gépek biztonsági mentését, győződjön meg róla, hogy az összes szükséges engedélyt megadja. További információ: [titkosított Azure-beli virtuális gép biztonsági mentése és visszaállítása](backup-azure-vms-encryption.md).

## <a name="usererrorguestagentstatusunavailable---vm-agent-unable-to-communicate-with-azure-backup"></a><a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable – A virtuálisgép-ügynök nem tud kommunikálni az Azure Backup szolgáltatással

**Hibakód**: UserErrorGuestAgentStatusUnavailable <br>
**Hibaüzenet**: a virtuális gép ügynöke nem tud kommunikálni Azure Backup<br>

Lehet, hogy az Azure VM-ügynök leállt, elavult, inkonzisztens állapotban van, vagy nincs telepítve. Ezek az állapotok megakadályozzák, hogy a Azure Backup szolgáltatás pillanatképeket indítson el.

- **Nyissa meg Azure Portal > vm > beállítások > Tulajdonságok ablaktáblán** > gondoskodjon arról, hogy a virtuális gép **állapota** **fusson** , és az **ügynök állapota** **kész**. Ha a virtuálisgép-ügynök leállt vagy inkonzisztens állapotban van, indítsa újra az ügynököt.<br>
  - Windows rendszerű virtuális gépek esetén a vendég ügynök újraindításához kövesse az alábbi [lépéseket](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms) .<br>
  - Linux rendszerű virtuális gépek esetén a vendég ügynök újraindításához kövesse az alábbi [lépéseket](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms) .
- **Nyissa meg Azure Portal > virtuálisgép-> beállítások > bővítmények** > győződjön meg arról, hogy az összes bővítmény üzembe helyezése **sikeres** állapotban van. Ha nem, kövesse az alábbi [lépéseket](#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state) a probléma megoldásához.

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError – Nem lehetett kommunikálni a virtuálisgép-ügynökkel a pillanatfelvétel állapotáról

**Hibakód**: GuestAgentSnapshotTaskStatusError<br>
**Hibaüzenet**: nem sikerült kommunikálni a virtuálisgép-ügynökkel a pillanatkép állapotához. <br>

Miután regisztrálta és beütemezte a virtuális gépet a Azure Backup szolgáltatáshoz, a biztonsági mentés elindítja a feladatot a virtuális gép biztonsági mentési bővítménnyel, hogy egy időponthoz tartozó pillanatképet készítsen. A következő feltételek bármelyike megakadályozhatja a pillanatkép aktiválását. Ha a pillanatkép nincs aktiválva, a biztonsági mentési hiba merülhet fel. Hajtsa végre a következő hibaelhárítási lépéseket a felsorolt sorrendben, majd próbálja megismételni a műveletet:  

**1. ok: [az ügynök telepítve van a virtuális gépen, de nem válaszol (Windows rendszerű virtuális gépek esetén)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  

**2. ok: [a virtuális gépen telepített ügynök elavult (Linux rendszerű virtuális gépek esetén)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

**3. ok: [a pillanatkép állapota nem olvasható be, vagy nem lehet pillanatképet készíteni](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken) .**

**4. ok: [a virtuális gép – ügynök konfigurációs beállításai nincsenek beállítva (Linux rendszerű virtuális gépek esetén)](#vm-agent-configuration-options-are-not-set-for-linux-vms)**

**5. ok: [az alkalmazás-ellenőrzési megoldás blokkolja IaaSBcdrExtension.exe](#application-control-solution-is-blocking-iaasbcdrextensionexe)**

## <a name="usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state"></a>UserErrorVmProvisioningStateFailed – A virtuális gép sikertelen kiépítési állapotban van

**Hibakód**: UserErrorVmProvisioningStateFailed<br>
**Hibaüzenet**: a virtuális gép sikertelen kiépítési állapotban van<br>

Ez a hiba akkor fordul elő, ha az egyik bővítmény hibája a virtuális gépet kiépítés sikertelen állapotba helyezi.<br>**Nyissa meg Azure Portal > virtuálisgép-> beállításait > bővítmények >-bővítmények állapota** elemre, és ellenőrizze, hogy az összes bővítmény üzembe helyezése **sikeres** volt-e. További információ: [kiépítési állapotok](../virtual-machines/states-billing.md).

- Ha bármelyik bővítmény hibás állapotban van, akkor az zavarhatja a biztonsági mentést. Győződjön meg arról, hogy a bővítmények problémái megoldódott, és próbálkozzon újra a biztonsági mentési művelettel.
- Ha a virtuális gép kiépítési állapota frissítési állapotban van, az zavarhatja a biztonsági mentést. Győződjön meg arról, hogy kifogástalan, és próbálkozzon újra a biztonsági mentési művelettel.

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached – Elérte a visszaállítási pont gyűjteményének korlátját

**Hibakód**: UserErrorRpCollectionLimitReached <br>
**Hibaüzenet**: elérte a visszaállítási pont gyűjtésének maximális korlátját. <br>

- Ez a probléma akkor fordulhat elő, ha a helyreállítási pont erőforráscsoport zárolása megakadályozza a helyreállítási pontok automatikus törlését.
- Ez a probléma akkor is előfordulhat, ha naponta több biztonsági mentést indítanak el. Jelenleg csak egy biztonsági mentést ajánlunk naponta, mivel az azonnali visszaállítási pontok megőrzése a beállított pillanatkép-megőrzési időtartam 1-5 nap, míg a virtuális gépeket csak 18 Instant RPs lehet egy adott időpontban társítani. <br>
- A helyreállítási pontok gyűjteményei és a virtuális gépek csoportjai közötti helyreállítási pontok száma nem haladhatja meg a 18-at. Új visszaállítási pont létrehozásához törölje a meglévő visszaállítási pontokat.

Javasolt művelet:<br>
A probléma megoldásához távolítsa el a virtuális gép erőforráscsoport zárolását, majd próbálja megismételni a műveletet a tisztítás elindításához.
> [!NOTE]
> A Backup szolgáltatás egy külön erőforráscsoportot hoz létre, mint a virtuális gép erőforráscsoport, amely a helyreállítási pontok gyűjtését tárolja. Azt tanácsoljuk, hogy ne zárolja a Backup szolgáltatás általi használatra létrehozott erőforráscsoportot. A Backup szolgáltatás által létrehozott erőforráscsoport elnevezési formátuma: AzureBackupRG_ `<Geo>` _ `<number>` . Például: *AzureBackupRG_northeurope_1*

**1. lépés: [a zárolás eltávolítása a visszaállítási pont erőforráscsoporthoz](#remove_lock_from_the_recovery_point_resource_group)** <br>
**2. lépés: a [visszaállítási pontok gyűjtésének tisztítása](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured – a biztonsági mentés nem rendelkezik megfelelő engedélyekkel a Key Vault számára a titkosított virtuális gépek biztonsági mentéséhez

**Hibakód**: UserErrorKeyvaultPermissionsNotConfigured <br>
**Hibaüzenet**: a biztonsági mentés nem rendelkezik megfelelő engedélyekkel a Key Vault számára a titkosított virtuális gépek biztonsági mentéséhez. <br>

Ahhoz, hogy a biztonsági mentési művelet sikeres legyen a titkosított virtuális gépeken, rendelkeznie kell engedéllyel a kulcstartó eléréséhez. Az engedélyek a [Azure Portalon](./backup-azure-vms-encryption.md) vagy a [powershellen](./backup-azure-vms-automation.md#enable-protection)keresztül állíthatók be.

## <a name="extensionsnapshotfailednonetwork---snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a><a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork – Nem sikerült elkészíteni a pillanatképet, mert nincs hálózati kapcsolat a virtuális gépen

**Hibakód**: ExtensionSnapshotFailedNoNetwork<br>
**Hibaüzenet**: a pillanatkép-művelet nem sikerült, mert nincs hálózati kapcsolat a virtuális gépen<br>

Miután regisztrálta és beütemezte a virtuális gépet a Azure Backup szolgáltatáshoz, a biztonsági mentés elindítja a feladatot a virtuális gép biztonsági mentési bővítménnyel, hogy egy időponthoz tartozó pillanatképet készítsen. A következő feltételek bármelyike megakadályozhatja a pillanatkép aktiválását. Ha a pillanatkép nincs aktiválva, a biztonsági mentési hiba merülhet fel. Hajtsa végre a következő hibaelhárítási lépést, majd próbálja megismételni a műveletet:

**[Nem lehet lekérdezni a pillanatkép állapotát, vagy nem lehet pillanatképet készíteni](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  

## <a name="extensionoperationfailedformanageddisks---vmsnapshot-extension-operation-failed"></a><a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtensionOperationFailedForManagedDisks – A VMSnapshot bővítmény művelete sikertelen volt

**Hibakód**: ExtensionOperationFailedForManagedDisks <br>
**Hibaüzenet**: a VMSnapshot-bővítmény művelete sikertelen volt.<br>

Miután regisztrálta és beütemezte a virtuális gépet a Azure Backup szolgáltatáshoz, a biztonsági mentés elindítja a feladatot a virtuális gép biztonsági mentési bővítménnyel, hogy egy időponthoz tartozó pillanatképet készítsen. A következő feltételek bármelyike megakadályozhatja a pillanatkép aktiválását. Ha a pillanatkép nincs aktiválva, a biztonsági mentési hiba merülhet fel. Hajtsa végre a következő hibaelhárítási lépéseket a felsorolt sorrendben, majd próbálja megismételni a műveletet:  
**1. ok: [a pillanatkép állapota nem olvasható be, vagy nem lehet pillanatképet készíteni](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken) .**  
**2. ok: [az ügynök telepítve van a virtuális gépen, de nem válaszol (Windows rendszerű virtuális gépek esetén)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**3. ok: [a virtuális gépen telepített ügynök elavult (Linux rendszerű virtuális gépek esetén)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed/BackUpOperationFailedV2 – A biztonsági mentés sikertelen, belső hiba

**Hibakód**: BackUpOperationFailed/BackUpOperationFailedV2 <br>
**Hibaüzenet**: a biztonsági mentés belső hiba miatt meghiúsult – próbálja megismételni a műveletet néhány perc múlva. <br>

Miután regisztrálta és beütemezte a virtuális gépet a Azure Backup szolgáltatáshoz, a biztonsági mentés a virtuális gép biztonsági mentési bővítménnyel való kommunikációval kezdeményezi az adott időponthoz tartozó pillanatképet. A következő feltételek bármelyike megakadályozhatja a pillanatkép aktiválását. Ha a pillanatkép nincs aktiválva, a biztonsági mentési hiba merülhet fel. Hajtsa végre a következő hibaelhárítási lépéseket a felsorolt sorrendben, majd próbálja megismételni a műveletet:  
**1. ok: [a virtuális gépen telepített ügynök, de nem válaszol (Windows rendszerű virtuális gépek esetén)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**2. ok: [a virtuális gépen telepített ügynök elavult (Linux rendszerű virtuális gépek esetén)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**3. ok: [a pillanatkép állapota nem olvasható be, vagy nem lehet pillanatképet készíteni](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken) .**  
**4. ok: a [Backup szolgáltatásnak nincs engedélye a régi visszaállítási pontok törlésére egy erőforráscsoport-zárolás miatt](#remove_lock_from_the_recovery_point_resource_group)**<br>

## <a name="usererrorunsupporteddisksize---the-configured-disk-sizes-is-currently-not-supported-by-azure-backup"></a>UserErrorUnsupportedDiskSize – a rendszer jelenleg nem támogatja a konfigurált lemezes méret (eke) t Azure Backup

**Hibakód**: UserErrorUnsupportedDiskSize <br>
**Hibaüzenet**: az Azure Backup jelenleg nem támogatja a konfigurált lemez méretét (ka) t. <br>

A biztonsági mentési művelet meghiúsulhat a 32 TB-nál nagyobb méretű virtuális gépek biztonsági mentésekor. Emellett a 4 TB-nál nagyobb méretű titkosított lemezek biztonsági mentése jelenleg nem támogatott. A lemez (ek) felosztásával győződjön meg arról, hogy a lemez mérete (i) kisebb vagy egyenlő, mint a támogatott korlát.

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress – nem sikerült elindítani a biztonsági mentést, mert folyamatban van egy másik biztonsági mentési művelet végrehajtása.

**Hibakód**: UserErrorBackupOperationInProgress <br>
**Hibaüzenet**: nem sikerült elindítani a biztonsági mentést, mert jelenleg folyamatban van egy másik biztonsági mentési művelet.<br>

A legutóbbi biztonsági mentési feladata sikertelen volt, mert folyamatban van egy meglévő biztonsági mentési művelet. Nem indíthat el új biztonsági mentési feladatot, amíg az aktuális feladatot be nem fejeződik. Győződjön meg arról, hogy a jelenleg folyamatban lévő biztonsági mentési művelet egy másik biztonsági mentési művelet elindítása vagy ütemezése előtt befejeződött. A biztonsági mentési feladatok állapotának megtekintéséhez hajtsa végre a következő lépéseket:

1. Jelentkezzen be a Azure Portalba, és válassza a **minden szolgáltatás** lehetőséget. Írja be a Recovery Servicest, és válassza a **Recovery Services**-tárolók lehetőséget. A Recovery Services-tárolók listája megjelenik.
2. Recovery Services-tárolók listájából válassza ki azt a tárat, amelyben a biztonsági mentés konfigurálva van.
3. A tároló irányítópultjának menüjében válassza a **biztonsági mentési feladatok** lehetőséget, amely megjeleníti az összes biztonsági mentési feladatot.
   - Ha folyamatban van egy biztonsági mentési feladat, várja meg, amíg befejeződik, vagy szakítsa meg a biztonsági mentési feladatot.
     - A biztonsági mentési feladat megszakításához kattintson a jobb gombbal a biztonsági mentési feladatokra, és válassza a **Mégse** vagy a [PowerShell](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob)használata lehetőséget.
   - Ha egy másik tárolóban újrakonfigurálta a biztonsági mentést, akkor győződjön meg arról, hogy a régi tárolóban nem fut biztonsági mentési feladat. Ha létezik, szakítsa meg a biztonsági mentési feladatot.
     - A biztonsági mentési feladat megszakításához kattintson a jobb gombbal a biztonsági mentési feladatokra, és válassza a **Mégse** vagy a [PowerShell](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob) használata lehetőséget.
4. Próbálkozzon újra a biztonsági mentési művelettel.

Ha az ütemezett biztonsági mentési művelet tovább tart, ütközik a következő biztonsági mentési konfigurációval, tekintse át az [ajánlott eljárásokat](backup-azure-vms-introduction.md#best-practices), a [biztonsági mentés teljesítményét](backup-azure-vms-introduction.md#backup-performance)és a [visszaállítási szempontot](backup-azure-vms-introduction.md#backup-and-restore-considerations).

## <a name="usererrorcrpreportedusererror---backup-failed-due-to-an-error-for-details-see-job-error-message-details"></a>UserErrorCrpReportedUserError – A biztonsági mentés egy hiba miatt nem sikerült. A részletekért lásd a feladat hibaüzenetének részleteit

**Hibakód**: UserErrorCrpReportedUserError <br>
**Hibaüzenet**: a biztonsági mentés hiba miatt meghiúsult. Részletekért lásd: feladatra vonatkozó hibaüzenet részletei.

Ezt a hibát a IaaS virtuális gépről kell jelenteni. A probléma gyökerének azonosításához nyissa meg a Recovery Services tároló beállításait. A **figyelés** szakaszban válassza a **biztonsági mentési feladatok** lehetőséget az állapot szűréséhez és megtekintéséhez. Válassza a **hibák** lehetőséget az alapul szolgáló hibaüzenet részleteinek áttekintéséhez. A hiba részletei lapon szereplő javaslatok alapján további műveleteket is végrehajthat.

## <a name="usererrorbcmdatasourcenotpresent---backup-failed-this-virtual-machine-is-not-actively-protected-by-azure-backup"></a>UserErrorBcmDatasourceNotPresent – a biztonsági mentés nem sikerült: Ez a virtuális gép (aktívan) nem védett Azure Backup

**Hibakód**: UserErrorBcmDatasourceNotPresent <br>
**Hibaüzenet**: a biztonsági mentés nem sikerült: ezt a virtuális gépet Azure Backup nem védi (aktívan).

Ellenőrizze, hogy az adott virtuális gép aktív-e (nem szüneteltetési állapotban) Azure Backup által védett. A probléma megoldásához ellenőrizze, hogy a virtuális gép aktív-e, majd próbálja megismételni a műveletet.

## <a name="causes-and-solutions"></a>Okok és megoldások

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Az ügynök telepítve van a virtuális gépen, de nem válaszol (Windows rendszerű virtuális gépek esetén)

#### <a name="solution-for-this-error"></a>Megoldás ehhez a hibához

Lehet, hogy a virtuálisgép-ügynök sérült, vagy a szolgáltatást leállították. A virtuálisgép-ügynök újratelepítése segíti a legújabb verzió beszerzését. A szolgáltatással folytatott kommunikáció újraindítását is lehetővé teszi.

1. Állapítsa meg, hogy a Windows Azure Guest Agent szolgáltatás fut-e a virtuálisgép-szolgáltatásokban (Services. msc). Próbálja meg újraindítani a Windows Azure Guest Agent szolgáltatást, és kezdeményezze a biztonsági mentést.
2. Ha a Windows Azure Guest Agent ügynök szolgáltatás nem látható a szolgáltatások területen, a Vezérlőpulton lépjen a **programok és szolgáltatások** elemre annak megállapításához, hogy telepítve van-e a Windows Azure Guest Agent szolgáltatás.
3. Ha a Windows Azure Guest Agent ügynök megjelenik a **programok és szolgáltatások szolgáltatásban**, távolítsa el a Windows Azure Guest Agent ügynököt.
4. Töltse le és telepítse az [Agent MSI legújabb verzióját](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). A telepítés befejezéséhez rendszergazdai jogosultságokkal kell rendelkeznie.
5. Ellenőrizze, hogy a Windows Azure Guest Agent szolgáltatás megjelenik-e a szolgáltatások között.
6. Igény szerinti biztonsági mentés futtatása:
   - A portálon válassza a **biztonsági mentés** lehetőséget.

Ellenőrizze azt is, hogy a [Microsoft .NET 4,5 telepítve van](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) -e a virtuális gépen. A .NET 4,5 szükséges ahhoz, hogy a virtuálisgép-ügynök kommunikáljon a szolgáltatással.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>A virtuális gépen telepített ügynök elavult (Linux rendszerű virtuális gépek esetén)

#### <a name="solution"></a>Megoldás

A Linux rendszerű virtuális gépek esetében a legtöbb ügynökkel kapcsolatos vagy kiterjesztéssel kapcsolatos hibát az elavult virtuálisgép-ügynököt érintő problémák okozzák. A probléma megoldásához kövesse az alábbi általános irányelveket:

1. Kövesse a Linux rendszerű [virtuális gép ügynökének frissítésével](../virtual-machines/extensions/update-linux-agent.md)kapcsolatos utasításokat.

   > [!NOTE]
   > Javasoljuk *, hogy csak* terjesztési tárházon keresztül frissítse az ügynököt. Nem javasoljuk, hogy közvetlenül a GitHubról töltse le az ügynököt, és frissítse azt. Ha a disztribúcióhoz tartozó legújabb ügynök nem érhető el, a telepítésével kapcsolatos útmutatásért forduljon az elosztási támogatáshoz. A legutóbbi ügynök kereséséhez nyissa meg a [Windows Azure Linux Agent](https://github.com/Azure/WALinuxAgent/releases) lapot a GitHub-tárházban.

2. A következő parancs futtatásával győződjön meg arról, hogy az Azure-ügynök fut a virtuális gépen: `ps -e`

   Ha a folyamat nem fut, indítsa újra a következő parancsok használatával:

   - Ubuntu esetén: `service walinuxagent start`
   - Egyéb disztribúciók esetén: `service waagent start`

3. [Konfigurálja az automatikus újraindítási ügynököt](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Futtasson egy új teszt biztonsági mentést. Ha a hiba továbbra is fennáll, Gyűjtse össze a következő naplókat a virtuális gépről:

   - /var/lib/waagent/*. XML
   - /var/log/waagent.log
   - /var/log/azure/*

Ha a waagent részletes naplózásra van szüksége, kövesse az alábbi lépéseket:

1. A/etc/waagent.conf fájlban keresse meg a következő sort: **részletes naplózás engedélyezése (y | n)**
2. Módosítsa a **naplókat. részletes** érték: *n* – *y*.
3. Mentse a változást, majd indítsa újra a waagent a jelen szakasz fentebb ismertetett lépéseinek végrehajtásával.

### <a name="vm-agent-configuration-options-are-not-set-for-linux-vms"></a>Nincsenek beállítva VM-Agent konfigurációs beállítások (Linux rendszerű virtuális gépek esetén)

A konfigurációs fájl (/etc/waagent.conf) szabályozza a waagent műveleteit. Konfigurációs fájl beállításai **bővítmények. az engedélyezés** értékének az **y** és a **kiépítés** beállításnak kell lennie. az ügynököt úgy kell beállítani, hogy a biztonsági mentés **automatikusan** működjön.
A VM-Agent konfigurációs fájl beállításainak teljes listájáért lásd: <https://github.com/Azure/WALinuxAgent#configuration-file-options>

### <a name="application-control-solution-is-blocking-iaasbcdrextensionexe"></a>Az alkalmazás-ellenőrzési megoldás blokkolja IaaSBcdrExtension.exe

Ha az [applockert](/windows/security/threat-protection/windows-defender-application-control/applocker/what-is-applocker) (vagy más alkalmazás-vezérlési megoldást) futtatja, és a szabályok közzétevők vagy elérési utak, akkor előfordulhat, hogy letiltják a **IaaSBcdrExtension.exe** végrehajtható fájl futtatását.

#### <a name="solution-to-this-issue"></a>A probléma megoldása

Zárja ki az `/var/lib` AppLocker (vagy más alkalmazás-vezérlő szoftver) elérési útját vagy **IaaSBcdrExtension.exe** végrehajtható fájlt.

### <a name="the-snapshot-status-cant-be-retrieved-or-a-snapshot-cant-be-taken"></a><a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Nem lehet lekérdezni a pillanatkép állapotát, vagy nem lehet pillanatképet készíteni

A virtuális gép biztonsági mentése egy pillanatkép-parancsnak a mögöttes Storage-fiókba való kiállítására támaszkodik. A biztonsági mentés sikertelen lehet, mert nem fér hozzá a Storage-fiókhoz, vagy mert a pillanatkép-feladat végrehajtása késleltetve van.

#### <a name="solution-for-this-issue"></a>A probléma megoldása

A következő feltételek miatt előfordulhat, hogy a pillanatkép-feladat meghiúsul:

| Ok | Megoldás |
| --- | --- |
| A virtuális gép állapota helytelenül van jelezve, mert a virtuális gép le van állítva RDP protokoll (RDP). | Ha az RDP-ben állítja le a virtuális gépet, a portálon ellenőrizze, hogy helyes-e a virtuális gép állapota. Ha nem megfelelő, állítsa le a virtuális gépet a portálon a virtuális gép irányítópultján lévő **Leállítás** lehetőség használatával. |
| A virtuális gép nem tudja lekérni a gazdagép vagy a háló címe DHCP-ről. | A IaaS virtuális gép biztonsági mentésének működéséhez engedélyezni kell a DHCP-t a vendégen belül. Ha a virtuális gép nem tudja lekérni a gazdagépet vagy a háló címeit a 245-es DHCP-válaszból, nem tölthet le és nem futtathat bővítményeket. Ha statikus magánhálózati IP-címmel kell rendelkeznie, konfigurálja a **Azure Portal** vagy a **PowerShell** használatával, és győződjön meg arról, hogy a virtuális gépen belül a DHCP-beállítás engedélyezve van. [További](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) információ statikus IP-cím beállításáról a PowerShell-lel.

### <a name="remove-lock-from-the-recovery-point-resource-group"></a><a name="remove_lock_from_the_recovery_point_resource_group"></a>A helyreállítási pont erőforráscsoporthoz tartozó zárolás eltávolítása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Lépjen a **minden erőforrás lehetőségre**, és válassza a visszaállítási pont gyűjteménye erőforráscsoportot a következő formátumban AzureBackupRG_ `<Geo>` _ `<number>` .
3. A zárolások megjelenítéséhez a **Beállítások** szakaszban válassza a **zárolások** lehetőséget.
4. A zárolás eltávolításához kattintson a három pontra, majd válassza a **Törlés** lehetőséget.

    ![Zárolás törlése](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean-up-restore-point-collection"></a><a name="clean_up_restore_point_collection"></a> Visszaállítási pont gyűjtésének törlése

A zárolás eltávolítását követően a visszaállítási pontokat törölni kell.

Ha törli a virtuális gép erőforráscsoportot, vagy maga a virtuális gép, a felügyelt lemezek azonnali visszaállítási pillanatképei aktívak maradnak, és a megőrzési csoportnak megfelelően lejárnak. Ha törölni szeretné a visszaállítási pont gyűjteményében tárolt azonnali visszaállítási pillanatképeket (ha nincs szüksége rájuk), törölje a visszaállítási pont gyűjtését az alábbi lépések szerint.

A visszaállítási pontok tisztításához kövesse az alábbi módszereket:<br>

- [A visszaállítási pontok gyűjtésének tisztítása igény szerinti biztonsági mentés futtatásával](#clean-up-restore-point-collection-by-running-on-demand-backup)<br>
- [Visszaállítási pont gyűjtésének törlése Azure Portal](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a><a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a>A visszaállítási pontok gyűjtésének tisztítása igény szerinti biztonsági mentés futtatásával

A zárolás eltávolítását követően indítson el egy igény szerinti biztonsági mentést. Ez a művelet biztosítja, hogy a visszaállítási pontok automatikusan törlődnek. Az igény szerinti művelet az első alkalommal sikertelen lesz. A visszaállítási pontok manuális törlése helyett azonban gondoskodni fog az automatikus tisztításról. A karbantartás után a következő ütemezett biztonsági mentésnek sikeresnek kell lennie.

> [!NOTE]
> Az automatikus tisztítás az igény szerinti biztonsági mentés néhány órája után is megtörténik. Ha az ütemezett biztonsági mentés továbbra is sikertelen, próbálja meg manuálisan törölni a visszaállítási pont gyűjteményét az [itt](#clean-up-restore-point-collection-from-azure-portal)felsorolt lépések segítségével.

#### <a name="clean-up-restore-point-collection-from-azure-portal-br"></a><a name="clean-up-restore-point-collection-from-azure-portal"></a>Visszaállítási pont gyűjtésének törlése Azure Portal <br>

Ha manuálisan szeretné törölni a visszaállítási pontok gyűjteményét, amely az erőforráscsoport zárolása miatt nem törlődik, próbálkozzon a következő lépésekkel:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A **központi** menüben válassza a **minden erőforrás** lehetőséget, válassza ki az erőforráscsoportot a következő formátumban AzureBackupRG_ `<Geo>` _ `<number>` , ahol a virtuális gép található.

    ![Válassza ki az erőforráscsoportot](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Válassza az erőforráscsoport lehetőséget, majd az **Áttekintés** panel jelenik meg.
4. Válassza a **rejtett típusok megjelenítése** lehetőséget az összes rejtett erőforrás megjelenítéséhez. Válassza ki a visszaállítási pontok gyűjteményeit a következő formátumban AzureBackupRG_ `<VMName>` _ `<number>` .

    ![A visszaállítási pont gyűjteményének kiválasztása](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Válassza a **Törlés** lehetőséget a visszaállítási pont gyűjteményének tisztításához.
6. Próbálja megismételni a biztonsági mentési műveletet.

> [!NOTE]
 >Ha az erőforrás (RP-gyűjtemény) nagy számú visszaállítási ponttal rendelkezik, akkor a portálról való törlés után időtúllépés és sikertelen lehet. Ez egy ismert CRP-probléma, amelyben az összes visszaállítási pont nem törlődik a meghatározott időn belül, és a művelet időtúllépést eredményez. A törlési művelet azonban általában két vagy három újrapróbálkozás után is sikeres.
