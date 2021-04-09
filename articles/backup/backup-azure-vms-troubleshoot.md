---
title: Azure-beli virtuális gépek biztonsági mentésével kapcsolatos hibák elhárítása
description: Ez a cikk az Azure-beli virtuális gépek biztonsági mentésével és visszaállításával kapcsolatos hibák elhárítását ismerteti.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 08/30/2019
ms.openlocfilehash: 2d09081533cdb2de5ee97cb000e9844b41a85ac3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105559366"
---
# <a name="troubleshooting-backup-failures-on-azure-virtual-machines"></a>Biztonsági mentési hibák elhárítása Azure-beli virtuális gépeken

A Azure Backup használata során észlelt hibák elhárítása az alábbi információkkal végezhető el:

## <a name="backup"></a>Backup

Ez a szakasz az Azure-beli virtuális gép biztonsági mentési műveletének hibáját ismerteti.

### <a name="basic-troubleshooting"></a>Alapszintű hibaelhárítás

* Győződjön meg arról, hogy a VM-ügynök (WA-ügynök) a [legújabb verzió](./backup-azure-arm-vms-prepare.md#install-the-vm-agent).
* Győződjön meg arról, hogy a Windows vagy Linux rendszerű virtuális gép operációsrendszer-verziója támogatott, tekintse meg a [IaaS virtuális gép biztonsági mentésének támogatási mátrixát](./backup-support-matrix-iaas.md).
* Ellenőrizze, hogy egy másik biztonsági mentési szolgáltatás nem fut-e.
  * Annak érdekében, hogy a pillanatképek kiterjesztésével kapcsolatos hibák ne legyenek elérhetők, [távolítsa el a bővítményeket a kényszerített újratöltéshez](./backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)
* Győződjön meg arról, hogy a virtuális gép rendelkezik internetkapcsolattal.
  * Győződjön meg arról, hogy egy másik biztonsági mentési szolgáltatás nem fut.
* A verzióban `Services.msc` ellenőrizze, hogy **fut**-e a **Windows Azure Guest Agent ügynök** szolgáltatás. Ha a **Windows Azure Guest Agent ügynök** szolgáltatás hiányzik, telepítse az [Azure-beli virtuális gépek biztonsági mentése egy Recovery Services-](./backup-azure-arm-vms-prepare.md#install-the-vm-agent)tárolóban.
* Az **Eseménynapló** tartalmazhat olyan biztonsági mentési hibákat, amelyek más biztonsági mentési termékekből származnak, például a Windows Server biztonsági másolatból, és a Azure Backup miatt nem. A következő lépések végrehajtásával megállapíthatja, hogy a probléma Azure Backup-e:
  * Ha hiba történt az eseményforrás vagy az üzenet bejegyzéseinek **biztonsági mentésével** kapcsolatban, ellenőrizze, hogy az Azure IaaS virtuális gépek biztonsági másolatai sikeresek voltak-e, és hogy egy visszaállítási pont lett-e létrehozva a kívánt pillanatkép-típussal.
  * Ha Azure Backup működik, akkor a probléma valószínűleg egy másik biztonsági mentési megoldás.
  * Íme egy példa egy Eseménynapló 517-es hibára, ahol Azure Backup működik, de a "Windows Server biztonsági másolat" sikertelen volt: ![ Windows Server biztonsági másolat sikertelen](media/backup-azure-vms-troubleshoot/windows-server-backup-failing.png)
  * Ha Azure Backup sikertelen, akkor keresse meg a megfelelő hibakódot a jelen cikkben található általános virtuális gép biztonsági mentési hibái című szakaszban.

## <a name="common-issues"></a>Gyakori problémák

Az alábbiakban az Azure-beli virtuális gépek biztonsági mentési hibáival kapcsolatos gyakori problémákat ismertetjük.

### <a name="vmrestorepointinternalerror---antivirus-configured-in-the-vm-is-restricting-the-execution-of-backup-extension"></a>VMRestorePointInternalError – a virtuális gépen konfigurált víruskereső korlátozza a biztonsági mentési bővítmény végrehajtását

Hibakód: VMRestorePointInternalError

Ha a biztonsági mentés időpontjában a **Eseménynapló az alkalmazás naplófájljai** megjelenítik az üzenet **hibás alkalmazásának nevét: IaaSBcdrExtension.exe** akkor a rendszer megerősíti, hogy a virtuális gépen konfigurált víruskereső korlátozza a biztonsági mentési bővítmény végrehajtását.
A probléma megoldásához zárja ki az alábbi könyvtárakat a víruskereső-konfigurációban, majd próbálja megismételni a biztonsági mentési műveletet.

* `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`
* `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`

### <a name="copyingvhdsfrombackupvaulttakinglongtime---copying-backed-up-data-from-vault-timed-out"></a>CopyingVHDsFromBackUpVaultTakingLongTime – a tárból érkező biztonsági másolatok adatainak másolása időtúllépéssel leállt

Hibakód: CopyingVHDsFromBackUpVaultTakingLongTime <br/>
Hibaüzenet: a biztonsági másolatból származó adatok másolása a tárból időtúllépés miatt megtörtént

Ez az átmeneti tárolási hibák vagy a nem elegendő Storage-fiók IOPS miatt fordulhat elő a Backup szolgáltatás számára az időtúllépési időszakon belül az adatoknak a tárolóba történő átviteléhez. Konfigurálja a virtuális gépek biztonsági mentését az [ajánlott eljárásokkal](backup-azure-vms-introduction.md#best-practices) , majd próbálja megismételni a biztonsági mentési műveletet.

### <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInDesirableState – A virtuális gép állapota nem teszi lehetővé a biztonsági mentés végrehajtását

Hibakód: UserErrorVmNotInDesirableState <br/>
Hibaüzenet: a virtuális gép nincs olyan állapotban, amely lehetővé teszi a biztonsági mentéseket.<br/>

A biztonsági mentési művelet meghiúsult, mert a virtuális gép hibás állapotban van. A sikeres biztonsági mentéshez a virtuális gépnek Fut, Leállítva vagy Leállítva (felszabadítva) állapotúnak kell lennie.

* Ha a virtuális gép átmeneti állapotban van a **Futtatás** és a **Leállítás** között, várjon, amíg az állapot megváltozhat. Ezután aktiválja a biztonsági mentési feladatot.
* Ha a virtuális gép Linux rendszerű virtuális gép, és a Security-Enhanced Linux kernel modult használja, zárja ki az Azure Linux-ügynök elérési útját a **/var/lib/waagent** , és győződjön meg arról, hogy a biztonsági mentési bővítmény telepítve van.

### <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed – nem sikerült befagyasztani a virtuális gép egy vagy több csatlakoztatási pontját, hogy egy fájlrendszerrel konzisztens pillanatképet készítsen

Hibakód: UserErrorFsFreezeFailed <br/>
Hibaüzenet: nem sikerült befagyasztani a virtuális gép egy vagy több csatlakoztatási pontját, hogy egy fájlrendszerrel konzisztens pillanatképet készítsen.

* A **umount** parancs használatával válassza le azokat az eszközöket, amelyekhez a fájlrendszer állapota nem lett törölve.
* Futtasson fájlrendszer-konzisztencia-ellenőrzéseket ezeken az eszközökön a **fsck** parancs használatával.
* Csatlakoztassa újra az eszközöket, és próbálkozzon újra a biztonsági mentési művelettel.</ol>

Ha nem tudja leválasztani az eszközöket, akkor a virtuális gép biztonsági mentési konfigurációját frissítheti bizonyos csatlakoztatási pontok figyelmen kívül hagyásával. Ha például a "/mnt/Resource" csatlakoztatási pont nem csatlakoztatható, és a virtuális gép biztonsági mentését okozza, a következőképpen frissítheti a virtuális gép biztonsági mentési konfigurációs fájljait a ```MountsToSkip``` tulajdonsággal.

```bash
cat /var/lib/waagent/Microsoft.Azure.RecoveryServices.VMSnapshotLinux-1.0.9170.0/main/tempPlugin/vmbackup.conf[SnapshotThread]
fsfreeze: True
MountsToSkip = /mnt/resource
SafeFreezeWaitInSeconds=600
```


### <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>ExtensionSnapshotFailedCOM/ExtensionInstallationFailedCOM/ExtensionInstallationFailedMDTC – a bővítmény telepítése/végrehajtása COM+ hiba miatt meghiúsult

Hibakód: ExtensionSnapshotFailedCOM <br/>
Hibaüzenet: a pillanatkép-művelet COM+ hiba miatt meghiúsult

Hibakód: ExtensionInstallationFailedCOM  <br/>
Hibaüzenet: a bővítmény telepítésének/műveletének végrehajtása COM+ hiba miatt meghiúsult

Hibakód: ExtensionInstallationFailedMDTC <br/>
Hibaüzenet: a bővítmény telepítése a következő hibával meghiúsult: "a COM+ nem tudott kommunikálni a Microsoft Distributed Transaction Coordinator <br/>

A biztonsági mentési művelet a Windows Service **com+** rendszeralkalmazás hibája miatt meghiúsult.  A probléma megoldásához kövesse az alábbi lépéseket:

* Próbálja meg elindítani vagy újraindítani a Windows Service **com+ Rendszeralkalmazást** (egy rendszergazda jogú parancssorból **– net start COMSysApp**).
* Győződjön meg arról, **Distributed Transaction coordinator** a szolgáltatás **hálózati szolgáltatás** fiókként fut. Ha nem, módosítsa a futtató **hálózati szolgáltatás** fiókját, és indítsa újra a **com+ rendszeralkalmazást**.
* Ha nem tudja újraindítani a szolgáltatást, a következő lépésekkel telepítse újra **Distributed Transaction coordinator** szolgáltatást:
  * Állítsa le az MSDTC szolgáltatást
  * Nyisson meg egy parancssort (cmd)
  * Futtassa a következő parancsot: `msdtc -uninstall`.
  * Futtassa a következő parancsot: `msdtc -install`.
  * Indítsa el az MSDTC szolgáltatást
* Indítsa el a Windows Service **com+ Rendszeralkalmazást**. A **com+ rendszeralkalmazás** elindítása után indítson el egy biztonsági mentési feladatot a Azure Portal.</ol>

### <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>ExtensionFailedVssWriterInBadState – Nem sikerült elkészíteni a pillanatképet, mert a VSS-írók nem megfelelő állapotban voltak

Hibakód: ExtensionFailedVssWriterInBadState <br/>
Hibaüzenet: a pillanatkép-művelet meghiúsult, mert a VSS-írók rossz állapotban voltak.

Ez a hiba azért fordul elő, mert a VSS-írók rossz állapotban voltak. A Azure Backup-bővítmények a VSS-írókkal együttműködve készítenek pillanatképeket a lemezekről. A probléma megoldásához kövesse az alábbi lépéseket:

1\. lépés: Indítsa újra a rossz állapotban lévő VSS-írókat.

* Egy rendszergazda jogú parancssorból futtassa a parancsot ```vssadmin list writers``` .
* A kimenet az összes VSS-írót, valamint azok állapotát is tartalmazza. Minden olyan VSS-író esetében, amelynek állapota nem **[1] Stabil**, indítsa újra a megfelelő VSS-író szolgáltatását.
* A szolgáltatás újraindításához futtassa a következő parancssort egy rendszergazdai jogú parancssorból: 

 ```net stop serviceName``` <br>
 ```net start serviceName```

> [!NOTE]
> Egyes szolgáltatások újraindítása hatással lehet az éles környezetre. Győződjön meg arról, hogy a jóváhagyási folyamat követhető, és a szolgáltatás az ütemezett leállás után újraindul.

2. lépés: Ha a VSS-írók újraindítása nem oldotta meg a problémát, futtassa a következő parancsot egy emelt szintű parancssorból (rendszergazdaként), hogy megakadályozza a szálak létrehozását a blob-pillanatképekhez.

```console
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotWithoutThreads /t REG_SZ /d True /f
```

3. lépés: Ha az 1. és a 2. lépés nem oldotta meg a problémát, akkor a hiba oka az lehet, hogy a VSS-írók korlátozott IOPS miatt időtúllépést okoznak.<br>

Az ellenőrzéshez keresse meg a ***rendszer-és Eseménynapló az alkalmazás naplóit*** , és keresse meg a következő hibaüzenetet:<br>
*Az árnyékmásolat-szolgáltató időkorlátja lejárt, miközben az írásokat az árnyékmásolat-kötetre másolta. Ezt valószínűleg egy alkalmazás vagy egy rendszerszolgáltatás túlzott tevékenysége okozza a köteten. Próbálkozzon újra később, ha a köteten végzett tevékenység csökken.*<br>

Megoldás:

* Győződjön meg arról, hogy a virtuális gépek lemezein át kell osztani a terhelést. Ez csökkenti az egyes lemezek terhelését. [A IOPs szabályozását a diagnosztikai metrikák tárolási szinten való engedélyezésével](/troubleshoot/azure/virtual-machines/performance-diagnostics#install-and-run-performance-diagnostics-on-your-vm)tekintheti meg.
* Módosítsa a biztonsági mentési szabályzatot a biztonsági mentések elvégzéséhez a csúcsidőben, amikor a virtuális gép terhelése a legalacsonyabb szinten van.
* Frissítse az Azure-lemezeket a magasabb IOPs támogatásához. [További információ](../virtual-machines/disks-types.md)

### <a name="extensionfailedvssserviceinbadstate---snapshot-operation-failed-due-to-vss-volume-shadow-copy-service-in-bad-state"></a>ExtensionFailedVssServiceInBadState – Nem sikerült elkészíteni a pillanatképet a VSS (Kötet árnyékmásolata) szolgáltatás nem megfelelő állapota miatt

Hibakód: ExtensionFailedVssServiceInBadState <br/>
Hibaüzenet: a pillanatkép-művelet nem sikerült, mert a VSS (Kötet árnyékmásolata) szolgáltatás helytelen állapotban van.

Ez a hiba azért fordul elő, mert a VSS szolgáltatás helytelen állapotban volt. A Azure Backup bővítmények a VSS szolgáltatással együttműködve készítenek pillanatképeket a lemezekről. A probléma megoldásához kövesse az alábbi lépéseket:

Indítsa újra a VSS (kötet árnyékmásolata) szolgáltatást.

* Keresse meg a Services.msc fájlt, és indítsa újra a „Kötet árnyékmásolata szolgáltatást”.<br>
(vagy)<br>
* Futtassa a következő parancsokat egy rendszergazdai jogú parancssorból:

 ```net stop VSS``` <br>
 ```net start VSS```

Ha a probléma továbbra is fennáll, indítsa újra a virtuális gépet az ütemezett leállás során.

### <a name="usererrorskunotavailable---vm-creation-failed-as-vm-size-selected-is-not-available"></a>UserErrorSkuNotAvailable – nem sikerült létrehozni a virtuális gépet, mert a kiválasztott virtuálisgép-méret nem érhető el

Hibakód: UserErrorSkuNotAvailable hibaüzenet: a virtuális gép létrehozása nem sikerült, mert a kiválasztott virtuálisgép-méret nem érhető el.

Ez a hiba azért fordul elő, mert a visszaállítási művelet során kiválasztott virtuális gép mérete nem támogatott. <br>

A probléma megoldásához a visszaállítási művelet során használja a [lemezek visszaállítása](./backup-azure-arm-restore-vms.md#restore-disks) lehetőséget. Ezeket a lemezeket használva hozzon létre egy virtuális gépet a [rendelkezésre álló támogatott](./backup-support-matrix-iaas.md#vm-compute-support) virtuálisgép-méretek közül a [PowerShell-parancsmagok](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks)használatával.

### <a name="usererrormarketplacevmnotsupported---vm-creation-failed-due-to-market-place-purchase-request-being-not-present"></a>UserErrorMarketPlaceVMNotSupported – a virtuális gép létrehozása nem sikerült, mert a Piactéri vásárlási kérelem nincs jelen

Hibakód: UserErrorMarketPlaceVMNotSupported hibaüzenet: a virtuális gép létrehozása nem sikerült, mert a Piactéri vásárlási kérelem nem található.

Azure Backup támogatja az Azure Marketplace-en elérhető virtuális gépek biztonsági mentését és helyreállítását. Ez a hiba akkor fordul elő, ha olyan virtuális gépet próbál visszaállítani (egy adott Tervtel/közzétevői beállítással), amely már nem érhető el az Azure Marketplace-en, további [információt itt talál](/legal/marketplace/participation-policy#offering-suspension-and-removal).

* A probléma megoldásához a visszaállítási művelet során használja a [lemezek visszaállítása](./backup-azure-arm-restore-vms.md#restore-disks) lehetőséget, majd a [PowerShell](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks) vagy az [Azure CLI](./tutorial-restore-disk.md) -PARANCSMAGok használatával hozza létre a virtuális gépet a legújabb Piactéri információkkal a virtuális géphez.
* Ha a közzétevő nem rendelkezik Piactéri információval, az adatlemezek segítségével lekérheti az adatokat, és csatolhatja őket egy meglévő virtuális géphez.

### <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure – hiba történt a biztonsági mentési bővítmény konfigurációjának elemzésekor

Hibakód: ExtensionConfigParsingFailure<br/>
Hibaüzenet: nem sikerült elemezni a biztonsági mentési bővítmény konfigurációját.

Ez a hiba a **következő** könyvtárának módosított engedélyei miatt fordul elő: **%SYSTEMDRIVE%\programdata\microsoft\crypto\rsa\machinekeys**.
Futtassa a következő parancsot, és győződjön meg arról, hogy a **következő** könyvtár engedélyei alapértelmezettek: `icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys` .

Az alapértelmezett engedélyek a következők:

* Mindenki: (R, W)
* Builtin\rendszergazda: (F)

Ha a **következő** címtárban az alapértelmezetttől eltérő engedélyek jelennek meg, kövesse az alábbi lépéseket az engedélyek megadásához, a tanúsítvány törléséhez és a biztonsági mentés elindításához:

1. Javítsa az engedélyeket a **következő** könyvtárában. Az Explorer biztonsági tulajdonságainak és a speciális biztonsági beállításoknak a címtárban való használatával állítsa vissza az engedélyeket az alapértelmezett értékekre. Távolítsa el az összes felhasználói objektumot, kivéve az alapértelmezett beállításokat a címtárból, és győződjön meg arról, hogy a **mindenki** engedély speciális hozzáféréssel rendelkezik a következő módon:

   * Mappa listázása/az adatolvasás
   * Attribútumok olvasása
   * Kiterjesztett attribútumok olvasása
   * Fájlok létrehozása/az adatírás
   * Mappák létrehozása/az adathozzáfűzés
   * Attribútumok írása
   * Kiterjesztett attribútumok írása
   * Olvasási engedélyek
2. Törölje az összes olyan tanúsítványt, ahol a **kiadás** a klasszikus üzemi modell vagy a **Windows Azure-beli CRP-tanúsítvány generátora**:

   * [Nyisson meg tanúsítványokat egy helyi számítógép-konzolon](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in).
   * A **személyes**  >  **tanúsítványok** területen törölje az összes olyan tanúsítványt, amely **számára a kiadás** a klasszikus üzemi modell vagy a **Windows Azure CRP-tanúsítvány generátora**.
3. Virtuális gép biztonsági mentési feladatainak elindítása.

### <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>A ExtensionStuckInDeletionState-bővítmény állapota nem támogatja a biztonsági mentési műveletet

Hibakód: ExtensionStuckInDeletionState <br/>
Hibaüzenet: a bővítmény állapota nem támogatja a biztonsági mentési műveletet

A biztonsági mentési művelet nem sikerült, mert inkonzisztens állapotú a biztonsági mentési bővítmény. A probléma megoldásához kövesse az alábbi lépéseket:

* Ellenőrizze, hogy a vendégügynök telepítve van-e és működik-e
* A Azure Portal lépjen a **virtuális gép**  >  **minden beállítás**  >  **bővítmények** elemre.
* Válassza ki a biztonsági mentési bővítmény VmSnapshot vagy VmSnapshotLinux, és válassza az **Eltávolítás** lehetőséget.
* A biztonsági mentési bővítmény törlését követően próbálja megismételni a biztonsági mentési műveletet.
* Ez a biztonsági mentési művelet a kívánt állapotban fogja telepíteni az új bővítményt

### <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>ExtensionFailedSnapshotLimitReachedError – a pillanatkép-készítési művelet nem sikerült, mert a csatolt lemezek némelyike túllépte a pillanatkép-korlátot

Hibakód: ExtensionFailedSnapshotLimitReachedError  <br/>
Hibaüzenet: a pillanatkép-művelet nem sikerült, mert a csatlakoztatott lemezek némelyike túllépte a pillanatkép-korlátot

A pillanatkép-művelet meghiúsult, mert a csatolt lemezek némelyike túllépte a pillanatkép-korlátot. Hajtsa végre az alábbi hibaelhárítási lépéseket, majd próbálja megismételni a műveletet.

* Törölje a nem szükséges lemezes blob-pillanatképeket. Ügyeljen arra, hogy ne töröljön lemezes blobokat. Csak pillanatkép-blobokat kell törölni.
* Ha a Soft-delete engedélyezve van a virtuális gép lemezének Storage-fiókjaiban, konfigurálja a helyreállítható törlési adatmegőrzést, hogy a meglévő Pillanatképek ne legyenek a maximálisan megengedettnél több időpontban.
* Ha a Azure Site Recovery engedélyezve van a biztonsági másolattal rendelkező virtuális gépen, hajtsa végre az alábbi lépéseket:

  * Győződjön meg arról, hogy a **isanysnapshotfailed** értéke hamis a/etc/Azure/vmbackup.conf
  * Más időpontban ütemezze Azure Site Recovery, így nem ütközik a biztonsági mentési művelettel.

### <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>ExtensionFailedTimeoutVMNetworkUnresponsive – a nem megfelelő virtuálisgép-erőforrások miatt sikertelen volt a pillanatkép-készítési művelet

Hibakód: ExtensionFailedTimeoutVMNetworkUnresponsive<br/>
Hibaüzenet: a nem megfelelő virtuálisgép-erőforrások miatt nem sikerült a pillanatkép-művelet.

A virtuális gépen a biztonsági mentési művelet sikertelen volt, mert a hálózati hívások késése a pillanatkép-művelet végrehajtása közben történt. A probléma megoldásához hajtsa végre az 1. lépést. Ha a probléma továbbra is fennáll, próbálkozzon a 2. és a 3. lépéssel.

**1. lépés**: pillanatkép létrehozása a gazdagépen keresztül

Futtassa a következő parancsot egy emelt szintű (rendszergazda) parancssorból:

```console
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

Ez gondoskodik róla, hogy a pillanatképek a gazdagépen keresztül készüljenek a vendég helyett. Próbálkozzon újra a biztonsági mentési művelettel.

**2. lépés**: próbálja meg módosítani a biztonsági mentési ütemtervet olyan időpontra, amikor a virtuális gép kevesebb terhelés alatt van (például kevesebb CPU vagy IOPS)

**3. lépés**: próbálja meg [növelni a virtuális gép méretét](../virtual-machines/windows/resize-vm.md) , és ismételje meg a műveletet.

### <a name="320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found"></a>320001, ResourceNotFound – a művelet nem hajtható végre, mert a virtuális gép már nem létezik/400094, BCMV2VMNotFound – a virtuális gép nem létezik, vagy nem található Azure-beli virtuális gép

Hibakód: 320001, ResourceNotFound <br/> Hibaüzenet: nem sikerült végrehajtani a műveletet, mert a virtuális gép már nem létezik. <br/> <br/> Hibakód: 400094, BCMV2VMNotFound <br/> Hibaüzenet: a virtuális gép nem létezik <br/>
Nem található Azure-beli virtuális gép.

Ez a hiba akkor fordul elő, ha az elsődleges virtuális gép törlődik, de a biztonsági mentési szabályzat továbbra is a virtuális gép biztonsági mentését keresi. A hiba elhárításához hajtsa végre a következő lépéseket:

* Hozza létre újra a virtuális gépet ugyanazzal a névvel és ugyanazzal az erőforráscsoport-névvel, a **Cloud Service-névvel**,<br>vagy
* Állítsa le a virtuális gép védelmét a biztonsági mentési adatok törlése nélkül vagy anélkül. További információ: a [virtuális gépek védelmének leállítása](backup-azure-manage-vms.md#stop-protecting-a-vm).</li></ol>

### <a name="usererrorbcmpremiumstoragequotaerror---could-not-copy-the-snapshot-of-the-virtual-machine-due-to-insufficient-free-space-in-the-storage-account"></a>UserErrorBCMPremiumStorageQuotaError – nem lehet másolni a virtuális gép pillanatképét, mert nincs elég szabad hely a Storage-fiókban.

Hibakód: UserErrorBCMPremiumStorageQuotaError<br/> Hibaüzenet: nem sikerült másolni a virtuális gép pillanatképét, mert nincs elég szabad hely a Storage-fiókban.

 A virtuális gépek biztonsági mentési verem v1-es verziójának prémium szintű virtuális gépei esetén a pillanatképet a Storage-fiókba másolja. Ez a lépés gondoskodik arról, hogy a biztonsági mentési felügyeleti forgalom, amely a pillanatképen működik, nem korlátozza az alkalmazás számára elérhető IOPS számát a prémium szintű lemezek használatával. <br><br>Javasoljuk, hogy a teljes Storage-fiók területének 50%-os, 17,5 TB-os számát foglalja le. Ezután a Azure Backup szolgáltatás átmásolhatja a pillanatképet a Storage-fiókba, és átviheti az adatait a Storage-fiókból a tárolóba.

### <a name="380008-azurevmoffline---failed-to-install-microsoft-recovery-services-extension-as-virtual-machine--is-not-running"></a>380008, AzureVmOffline – nem sikerült telepíteni a Microsoft Recovery Services-bővítményt, mert a virtuális gép nem fut

Hibakód: 380008, AzureVmOffline <br/> Hibaüzenet: nem sikerült telepíteni a Microsoft Recovery Services-bővítményt, mert a virtuális gép nem fut

A VM-ügynök az Azure Recovery Services bővítmény előfeltétele. Telepítse az Azure Virtual Machine Agent ügynököt, és indítsa újra a regisztrációs műveletet. <br> <ol> <li>Ellenőrizze, hogy a virtuális gép ügynöke megfelelően van-e telepítve. <li>Győződjön meg arról, hogy a virtuális gép konfigurációjának jelzője helyesen van beállítva.</ol> További információ a virtuálisgép-ügynök telepítéséről és a virtuálisgép-ügynök telepítésének ellenőrzéséről.

### <a name="extensionsnapshotbitlockererror---the-snapshot-operation-failed-with-the-volume-shadow-copy-service-vss-operation-error"></a>ExtensionSnapshotBitlockerError – a pillanatkép-művelet a Kötet árnyékmásolata szolgáltatás (VSS) művelet hibája miatt meghiúsult

Hibakód: ExtensionSnapshotBitlockerError <br/> Hibaüzenet: a pillanatkép-művelet a Kötet árnyékmásolata szolgáltatás (VSS) művelet hibája miatt sikertelen volt, mert az **BitLocker meghajtótitkosítás zárolta a meghajtót. A meghajtót fel kell oldani a Vezérlőpultról.**

Kapcsolja ki a BitLockert a virtuális gépen lévő összes meghajtón, és ellenőrizze, hogy megoldódott-e a VSS-probléma.

### <a name="vmnotindesirablestate---the-vm-isnt-in-a-state-that-allows-backups"></a>VmNotInDesirableState – a virtuális gép nincs olyan állapotban, amely lehetővé teszi a biztonsági mentéseket

Hibakód: VmNotInDesirableState <br/> Hibaüzenet: a virtuális gép nincs olyan állapotban, amely lehetővé teszi a biztonsági mentéseket.

* Ha a virtuális gép átmeneti állapotban van a **Futtatás** és a **Leállítás** között, várjon, amíg az állapot megváltozhat. Ezután aktiválja a biztonsági mentési feladatot.
* Ha a virtuális gép Linux rendszerű virtuális gép, és a Security-Enhanced Linux kernel modult használja, zárja ki az Azure Linux-ügynök elérési útját a **/var/lib/waagent** , és győződjön meg arról, hogy a biztonsági mentési bővítmény telepítve van.

* A virtuálisgép-ügynök nincs jelen a virtuális gépen: <br>Telepítse az előfeltételt és a virtuálisgép-ügynököt. Ezután indítsa újra a műveletet. | További információ a [VM-ügynök telepítéséről és a virtuálisgép-ügynök telepítésének ellenőrzéséről](#vm-agent).

### <a name="extensionsnapshotfailednosecurenetwork---the-snapshot-operation-failed-because-of-failure-to-create-a-secure-network-communication-channel"></a>ExtensionSnapshotFailedNoSecureNetwork – a pillanatkép-művelet sikertelen volt, mert nem sikerült létrehozni a biztonságos hálózati kommunikációs csatornát

Hibakód: ExtensionSnapshotFailedNoSecureNetwork <br/> Hibaüzenet: a pillanatkép-művelet sikertelen volt, mert nem sikerült létrehozni a biztonságos hálózati kommunikációs csatornát.

* A **regedit.exe** futtatásával nyissa meg a Beállításszerkesztőt egy emelt szintű módban.
* Azonosítsa a rendszeren lévő .NET-keretrendszer összes verzióját. A **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft** beállításkulcs hierarchiájában jelennek meg.
* A beállításkulcsban található minden egyes .NET-keretrendszerhez adja hozzá a következő kulcsot: <br> **Alatt "= DWORD: 00000001**. </ol>

### <a name="extensionvcredistinstallationfailure---the-snapshot-operation-failed-because-of-failure-to-install-visual-c-redistributable-for-visual-studio-2012"></a>ExtensionVCRedistInstallationFailure – a pillanatkép-művelet sikertelen volt, mert hiba történt a Visual C++ terjeszthető csomagjának telepítése során a Visual Studio 2012-ben

Hibakód: ExtensionVCRedistInstallationFailure <br/> Hibaüzenet: a pillanatkép-művelet sikertelen volt, mert a Visual C++ újraterjeszthető csomagjának telepítése sikertelen volt a Visual Studio 2012-ben.

* Navigáljon a `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion` vcredist2013_x64ra, és telepítse a következőt:.<br/>Győződjön meg arról, hogy a beállításjegyzék-kulcs értéke, amely lehetővé teszi, hogy a szolgáltatás telepítése a megfelelő értékre legyen állítva. Ez azt eredményezi, hogy a **kezdő** értéket **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** **3** értékre, nem pedig **4** értékre állítja. <br><br>Ha továbbra is problémái vannak a telepítéssel, indítsa újra a telepítési szolgáltatást az msiexec **/UNREGISTER** , majd az **msiexec/Register** egy rendszergazda jogú parancssorból való futtatásával.
* Ellenőrizze az eseménynaplót, és ellenőrizze, hogy a hozzáféréshez kapcsolódó problémák vannak-e. Például: *termék: Microsoft Visual C++ 2013 x64 minimális futtatókörnyezet – 12.0.21005 – hiba 1401. nem sikerült létrehozni a kulcsot: Software\Classes.  5. rendszerhiba.  Ellenőrizze, hogy van-e megfelelő engedélye a kulcs eléréséhez, vagy forduljon a technikai tanácsadási csoporthoz.* <br><br> Győződjön meg arról, hogy a rendszergazda vagy a felhasználói fiók rendelkezik a **HKEY_LOCAL_MACHINE\SOFTWARE\Classes** beállításkulcs frissítéséhez szükséges engedélyekkel. Adja meg a megfelelő engedélyeket, és indítsa újra a Windows Azure Guest Agent ügynököt.<br><br> <li> Ha víruskereső termékek vannak érvényben, győződjön meg arról, hogy a megfelelő kizárási szabályokkal rendelkeznek a telepítés engedélyezéséhez.

### <a name="usererrorrequestdisallowedbypolicy---an-invalid-policy-is-configured-on-the-vm-which-is-preventing-snapshot-operation"></a>UserErrorRequestDisallowedByPolicy – Érvénytelen szabályzat van konfigurálva a virtuális gépen, amely meggátolja a Pillanatkép műveletet

Hibakód: UserErrorRequestDisallowedByPolicy <BR> Hibaüzenet: érvénytelen házirend van konfigurálva a virtuális gépen, amely megakadályozza a pillanatkép-műveletet.

Ha van olyan Azure Policy, amely [a környezetében található címkéket szabályozza](../governance/policy/tutorials/govern-tags.md), érdemes lehet a szabályzatot egy [megtagadási hatásról](../governance/policy/concepts/effects.md#deny) [módosítani](../governance/policy/concepts/effects.md#modify), vagy a [Azure Backup által igényelt elnevezési séma](./backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines)alapján manuálisan létrehozni az erőforráscsoportot.

## <a name="jobs"></a>Feladatok

| A hiba részletei | Áthidaló megoldás |
| --- | --- |
| Ez a feladattípus nem támogatja a megszakítást: <br>Várjon, amíg a feladatok befejeződik. |Nincsenek |
| A feladat nem törölhető állapotban van: <br>Várjon, amíg a feladatok befejeződik. <br>**vagy**<br> A kijelölt feladat nem törölhető állapotban van: <br>Várjon, amíg a feladatok befejeződik. |Valószínű, hogy a feladatot majdnem befejezték. Várjon, amíg a feladatok befejeződik.|
| A biztonsági mentés nem tudja megszakítani a feladatot, mert nincs folyamatban: <br>A megszakítás csak folyamatban lévő feladatok esetén támogatott. Próbálkozzon egy folyamatban lévő feladat megszakításával. |Ez a hiba átmeneti állapot miatt fordul elő. Várjon egy percet, és ismételje meg a megszakítási műveletet. |
| A biztonsági mentés nem tudta megszakítani a feladatot: <br>Várjon, amíg a feladatok befejeződik. |Nincsenek |

## <a name="restore"></a>Visszaállítás

### <a name="disks-appear-offline-after-file-restore"></a>A lemezek offline állapotban jelennek meg a fájl visszaállítása után

Ha a visszaállítás után észleli, hogy a lemezek offline állapotban vannak:

* Ellenőrizze, hogy a parancsfájlt futtató gép megfelel-e az operációs rendszer követelményeinek. [További információk](./backup-azure-restore-files-from-vm.md#step-3-os-requirements-to-successfully-run-the-script).  
* Győződjön meg arról, hogy nem áll helyre visszaállítás ugyanarra a forrásra, [További információ](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

### <a name="usererrorinstantrpnotfound---restore-failed-because-the-snapshot-of-the-vm-was-not-found"></a>UserErrorInstantRpNotFound – a visszaállítás nem sikerült, mert a virtuális gép pillanatképe nem található

Hibakód: UserErrorInstantRpNotFound <br>
Hibaüzenet: a visszaállítás nem sikerült, mert a virtuális gép pillanatképe nem található. A pillanatképet törölték, ellenőrizze.<br>

Ez a hiba akkor fordul elő, ha olyan helyreállítási pontról próbál visszaállítani, amely nem lett átadva a tárolóba, és törölték a pillanatkép fázisában. 
<br>
A probléma megoldásához próbálja meg visszaállítani a virtuális gépet egy másik visszaállítási pontról.<br>

#### <a name="common-errors"></a>Gyakori hibák 
| A hiba részletei | Áthidaló megoldás |
| --- | --- |
| A Restore művelet belső felhőalapú hibával meghiúsult. |<ol><li>A felhőalapú szolgáltatás, amelyre a visszaállítást végzi, DNS-beállításokkal van konfigurálva. A következőket tekintheti meg: <br>**$Deployment = Get-AzureDeployment-szolgáltatásnév "szolgáltatásnév"-slot "Production" Get-AzureDns-DnsSettings $Deployment. DnsSettings**.<br>Ha a **címe** konfigurálva van, a rendszer konfigurálja a DNS-beállításokat.<br> <li>A felhőalapú szolgáltatás, amelyre a visszaállítást végzi, a **foglalt IP**-sel van konfigurálva, a felhőalapú szolgáltatásban lévő meglévő virtuális gépek pedig leállított állapotban vannak. A következő PowerShell-parancsmagok használatával megtekintheti, hogy a Cloud Service fenntartott-e egy IP-címet: **$Deployment = Get-AzureDeployment-szolgáltatásnév "szolgáltatásnév"-slot "Production" $DEP. ReservedIPName**. <br><li>Egy virtuális gépet a következő speciális hálózati konfigurációkkal próbál visszaállítani ugyanazon a felhőalapú szolgáltatásban: <ul><li>Virtuális gépek a terheléselosztó konfigurációjában, belső és külső.<li>Több fenntartott IP-címmel rendelkező virtuális gépek. <li>Több hálózati adapterrel rendelkező virtuális gépek. </ul><li>Válasszon egy új felhőalapú szolgáltatást a felhasználói felületen, vagy tekintse meg a speciális hálózati konfigurációval rendelkező virtuális gépek [visszaállítási szempontjait](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) .</ol> |
| A kiválasztott DNS-név már használatban van: <br>Adjon meg másik DNS-nevet, és próbálkozzon újra. |Ez a DNS-név a felhőalapú szolgáltatás nevére hivatkozik, általában a **. cloudapp.net** végződéssel. A névnek egyedinek kell lennie. Ha ezt a hibaüzenetet kapja, a visszaállítás során másik virtuálisgép-nevet kell választania. <br><br> Ez a hiba csak a Azure Portal felhasználói számára jelenik meg. A PowerShell-alapú visszaállítási művelet sikeres, mert csak a lemezeket állítja vissza, és nem hozza létre a virtuális gépet. A hiba akkor jelenik meg, ha a virtuális gépet explicit módon hozza létre a lemez-visszaállítási művelet után. |
| A virtuális hálózat megadott konfigurációja nem megfelelő: <br>Adjon meg másik virtuális hálózati konfigurációt, és próbálkozzon újra. |Nincsenek |
| A megadott felhőalapú szolgáltatás olyan fenntartott IP-címet használ, amely nem felel meg a visszaállítani kívánt virtuális gép konfigurációjának: <br>Olyan felhőalapú szolgáltatást válasszon, amely nem használ fenntartott IP-címet. Vagy válasszon egy másik helyreállítási pontot a visszaállításhoz. |Nincsenek |
| A felhőalapú szolgáltatás elérte a bemeneti végpontok számának korlátját: <br>Próbálja megismételni a műveletet egy másik felhőalapú szolgáltatás megadásával vagy egy meglévő végpont használatával. |Nincsenek |
| A Recovery Services-tár és a célként megadott Storage-fiók két különböző régióban található: <br>Győződjön meg arról, hogy a visszaállítási műveletben megadott Storage-fiók ugyanabban az Azure-régióban található, mint a Recovery Services-tároló. |Nincsenek |
| A visszaállítási művelethez megadott Storage-fiók nem támogatott: <br>Csak a helyileg redundáns vagy földrajzilag redundáns replikációs beállításokkal rendelkező alapszintű vagy standard szintű Storage-fiókok támogatottak. Válasszon egy támogatott Storage-fiókot. |Nincsenek |
| A visszaállítási művelethez megadott Storage-fiók típusa nincs online állapotban: <br>Győződjön meg arról, hogy a visszaállítási műveletben megadott Storage-fiók online állapotban van. |Ez a hiba az Azure Storage-ban vagy kimaradás miatti átmeneti hiba miatt fordulhat elő. Válasszon másik Storage-fiókot. |
| Elérte az erőforráscsoport-kvótát: <br>Töröljön néhány erőforráscsoportot a Azure Portal, vagy forduljon az Azure támogatási szolgálatához, és növelje a határértékeket. |Nincsenek |
| A kiválasztott alhálózat nem létezik: <br>Válasszon egy létező alhálózatot. |Nincsenek |
| A Backup szolgáltatásnak nincs engedélye az erőforrásokhoz való hozzáférésre az előfizetésében. |A hiba megoldásához először állítsa vissza a lemezeket a [biztonsági másolatba mentett lemezek visszaállítása](backup-azure-arm-restore-vms.md#restore-disks)című cikkben ismertetett lépések segítségével. Ezután használja a [virtuális gép létrehozása helyreállított lemezekről](backup-azure-vms-automation.md#restore-an-azure-vm)című témakör PowerShell-lépéseit. |

## <a name="backup-or-restore-takes-time"></a>A biztonsági mentés vagy a visszaállítás időt vesz igénybe

Ha a biztonsági mentés több mint 12 órát vesz igénybe, vagy a visszaállítás 6 óránál hosszabb időt vesz igénybe, tekintse át az [ajánlott eljárásokat](backup-azure-vms-introduction.md#best-practices)és a [teljesítménnyel kapcsolatos szempontokat](backup-azure-vms-introduction.md#backup-performance) .

## <a name="vm-agent"></a>VM-ügynök

### <a name="set-up-the-vm-agent"></a>A virtuálisgép-ügynök beállítása

A virtuálisgép-ügynök általában már jelen van az Azure Galleryből létrehozott virtuális gépeken. A helyszíni adatközpontokból áttelepített virtuális gépek azonban nem lesznek telepítve a virtuálisgép-ügynökkel. Ezekhez a virtuális gépekhez explicit módon telepíteni kell a virtuálisgép-ügynököt.

#### <a name="windows-vms---set-up-the-agent"></a>Windows rendszerű virtuális gépek – az ügynök beállítása

* Töltse le és telepítse az [ügynök MSI-t](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). A telepítés befejezéséhez rendszergazdai jogosultságok szükségesek.
* A klasszikus üzemi modellel létrehozott virtuális gépek esetében [frissítse a VM tulajdonságot](/troubleshoot/azure/virtual-machines/install-vm-agent-offline#use-the-provisionguestagent-property-for-classic-vms) , hogy jelezze, hogy telepítve van-e az ügynök. Ez a lépés nem szükséges Azure Resource Manager virtuális gépekhez.

#### <a name="linux-vms---set-up-the-agent"></a>Linux rendszerű virtuális gépek – az ügynök beállítása

* Telepítse az ügynök legújabb verzióját a terjesztési tárházból. A csomag nevével kapcsolatos részletekért tekintse meg a [Linux-ügynök tárházát](https://github.com/Azure/WALinuxAgent).
* A klasszikus üzemi modellel létrehozott virtuális gépek esetében [frissítse a VM tulajdonságot](/troubleshoot/azure/virtual-machines/install-vm-agent-offline#use-the-provisionguestagent-property-for-classic-vms) , és ellenőrizze, hogy telepítve van-e az ügynök. Ez a lépés nem szükséges a Resource Manager virtuális gépekhez.

### <a name="update-the-vm-agent"></a>A virtuálisgép-ügynök frissítése

#### <a name="windows-vms---update-the-agent"></a>Windows rendszerű virtuális gépek – az ügynök frissítése

* A VM-ügynök frissítéséhez telepítse újra a virtuálisgép- [ügynök bináris fájljait](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Az ügynök frissítése előtt győződjön meg arról, hogy a virtuálisgép-ügynök frissítése során nem történik biztonsági mentési művelet.

#### <a name="linux-vms---update-the-agent"></a>Linux rendszerű virtuális gépek – az ügynök frissítése

* A Linux rendszerű virtuális gép ügynökének frissítéséhez kövesse a Linux rendszerű [virtuális gép ügynökének frissítése](../virtual-machines/extensions/update-linux-agent.md?toc=/azure/virtual-machines/linux/toc.json)című cikk utasításait.

    > [!NOTE]
    > Mindig a terjesztési tárházat használja az ügynök frissítéséhez.

    Ne töltse le az ügynök kódját a GitHubról. Ha a legújabb ügynök nem érhető el a terjesztéshez, forduljon a terjesztési támogatáshoz a legújabb ügynök beolvasására vonatkozó utasításokért. A legújabb [Windows Azure Linux-ügynök](https://github.com/Azure/WALinuxAgent/releases) adatait is megtekintheti a GitHub-tárházban.

### <a name="validate-vm-agent-installation"></a>Virtuálisgép-ügynök telepítésének ellenőrzése

Ellenőrizze a virtuálisgép-ügynök verzióját a Windows rendszerű virtuális gépeken:

1. Jelentkezzen be az Azure-beli virtuális gépre, és navigáljon a **C:\WindowsAzure\Packages** mappába. Keresse meg a **WaAppAgent.exe** fájlt.
2. Kattintson a jobb gombbal a fájlra, és válassza a **Tulajdonságok menüpontot**. Ezután válassza a **részletek** lapot. A **termék verzió** mezőjének 2.6.1198.718 vagy magasabbnak kell lennie.

## <a name="troubleshoot-vm-snapshot-issues"></a>Virtuális gépek pillanatképpel kapcsolatos problémáinak elhárítása

A virtuális gép biztonsági mentése a pillanatkép-parancsok alapjául szolgáló tárolóra támaszkodik. Ha nem fér hozzá a tárolóhoz, vagy késések vannak a pillanatképek futtatásához, a biztonsági mentési feladat sikertelen lehet. A következő feltételek okozhatnak pillanatkép-feladathoz tartozó hibát:

* **A SQL Server biztonsági mentéssel konfigurált virtuális gépek a pillanatkép-feladatok késleltetését okozhatják**. Alapértelmezés szerint a virtuális gép biztonsági mentése létrehoz egy VSS teljes biztonsági mentést a Windows rendszerű virtuális gépeken. Azok a virtuális gépek, amelyek SQL Server futtatnak SQL Server biztonsági mentést, pillanatkép-késéseket tapasztalhatnak. Ha a pillanatkép-késések biztonsági mentési hibákat okoznak, állítsa be a következő beállításkulcsot:

   ```console
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

* **A virtuális gép állapota helytelenül van jelezve, mert a virtuális gép RDP-ben le van állítva**. Ha a távoli asztal használatával állítja le a virtuális gépet, ellenőrizze, hogy helyes-e a virtuális gép állapota a portálon. Ha az állapot nem megfelelő, a virtuális gép leállításához használja a portál virtuálisgép-irányítópultjának **Leállítás** lehetőségét.
* **Ha négynél több virtuális gép osztozik ugyanazzal a felhőalapú szolgáltatással, a virtuális gépeket több biztonsági mentési házirendben is elosztja**. A biztonsági mentés időpontjának felosztása, így a több mint négy virtuális gép biztonsági mentése egyidőben megkezdődik. Próbálja meg elkülöníteni a házirendek indítási időpontját legalább egy órával.
* **A virtuális gép magas processzoron vagy memórián fut**. Ha a virtuális gép nagy memórián vagy CPU-használaton fut, több mint 90 százalékkal, a pillanatkép-feladat várólistára kerül és késleltetve lesz. Végül túllépi az időkorlátot. Ha ez a probléma történik, próbálkozzon egy igény szerinti biztonsági mentéssel.

## <a name="networking"></a>Hálózatkezelés

A DHCP-t engedélyezni kell a vendégen a IaaS virtuális gép biztonsági mentésének működéséhez. Ha statikus magánhálózati IP-címmel kell rendelkeznie, konfigurálja a Azure Portal vagy a PowerShell használatával. Győződjön meg arról, hogy a virtuális gépen belül a DHCP-beállítás engedélyezve van.
További információ arról, hogyan állítható be statikus IP-cím a PowerShell használatával:

* [Statikus belső IP-cím hozzáadása meglévő virtuális géphez](/powershell/module/az.network/set-aznetworkinterfaceipconfig#description)
* [Hálózati adapterhez rendelt magánhálózati IP-cím kiosztási módszerének módosítása](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)