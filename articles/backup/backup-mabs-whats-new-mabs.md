---
title: A Microsoft Azure Backup Server újdonságai
description: A Microsoft Azure Backup-kiszolgáló továbbfejlesztett biztonsági mentési lehetőségeket biztosít a virtuális gépek, fájlok és mappák, munkaterhelések és egyebek védelméhez.
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: 39050d0f658e29b82f270f1fe53026e2fb80bfa1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91332763"
---
# <a name="whats-new-in-microsoft-azure-backup-server-mabs"></a>A Microsoft Azure Backup-kiszolgáló újdonságai (MABS)

## <a name="whats-new-in-mabs-v3-ur1"></a>Az MABS v3 UR1 újdonságai

A Microsoft Azure Backup Server (MABS) 3. verziója UR1 a legújabb frissítés, amely kritikus hibajavításokat és egyéb funkciókat és fejlesztéseket tartalmaz. A rögzített hibák listájának és a MABS v3 UR1 telepítési utasításának megtekintéséhez lásd a TUDÁSBÁZIS [4534062](https://support.microsoft.com/help/4534062)-os számú cikkét.

>[!NOTE]
>Az 32 bites védelmi ügynök támogatása elavult a MABS v3 UR1. Lásd: [32 bites védelmi ügynök elavulása](#32-bit-protection-agent-deprecation).

### <a name="faster-backups-with-tiered-storage-using-ssds"></a>Gyorsabb biztonsági mentés többplatformos tárolással SSD-k használatával

A MABS v2 [modern biztonsági másolati tárhely](backup-mabs-add-storage.md) (MBS), ami javítja a tárterület kihasználtságát és a teljesítményt. Az MBS a ReFS használja alapul szolgáló fájlrendszerként, és úgy van kialakítva, hogy a hibrid tárolókat, például a többszintű tárolást használja.

Az MB-os méretezés és teljesítmény elérése érdekében javasoljuk, hogy a Flash Storage (SSD) kis százalékát (a teljes tárterület 4%-át) használja a MABS v3 UR1, a DPM HDD-tárolóval együtt. A MABS v3 UR1 és a többplatformos tárolás 50-70%-kal gyorsabb biztonsági mentést biztosít. A többplatformos tárolás konfigurálásának lépéseiért tekintse meg a DPM ( [MB) beállítását a Tiered Storage](/system-center/dpm/add-storage#set-up-mbs-with-tiered-storage) .

### <a name="support-for-refs-volumes"></a>ReFS-kötetek támogatása

A MABS v3 UR1 segítségével biztonsági mentést készíthet a ReFS köteten telepített ReFS-kötetekről és munkaterhelésekről. A ReFS-köteteken üzembe helyezett következő számítási feladatok biztonsági mentését végezheti el:

* Operációs rendszer (64 bit): Windows Server 2019, 2016, 2012 R2, 2012.
* SQL Server: SQL Server 2019, SQL Server 2017, 2016.
* Exchange: Exchange 2019, 2016.
* SharePoint: SharePoint 2019, 2016, legújabb SZERVIZCSOMAGgal.

>[!NOTE]
> A ReFS-köteten tárolt Hyper-V virtuális gépek biztonsági mentését a MABS v3 támogatja

>FONTOS Azonosítottank néhány problémát a deduplikált ReFS-kötetek biztonsági mentésével kapcsolatban. Dolgozunk ezen a megoldáson, és frissítjük ezt a szakaszt, amint elérhetővé tettük a javítást. Addig is eltávolítjuk a deduplikált ReFS-kötetek biztonsági mentésének támogatását a MABSv3-UR1.

### <a name="azure-vmware-solution-protection-support"></a>Azure VMware-megoldás védelme – támogatás

Az MABS v3 UR1 mostantól az [Azure VMware-megoldásban](../azure-vmware/index.yml)üzembe helyezett virtuális gépeket is védetté teheti.

### <a name="vmware-parallel-backups"></a>VMware párhuzamos biztonsági mentések

A MABS v3 UR1 egyetlen védelmi csoportban lévő összes VMware virtuális gép biztonsági másolata párhuzamosan fog futni, ami 25%-kal gyorsabb virtuális gépek biztonsági mentését eredményezi.
A MABS korábbi verzióival a párhuzamos biztonsági mentések csak a védelmi csoportokon keresztül lettek elvégezve. A MABS v3 UR1 párhuzamosan futnak a VMware Delta replikációs feladatok. Alapértelmezés szerint a párhuzamosan futtatandó feladatok száma 8. További információ a [VMware Parallel Backup](backup-azure-backup-server-vmware.md#vmware-parallel-backups)szolgáltatásról.

### <a name="disk-exclusion-for-vmware-vm-backup"></a>A lemezek kizárása a VMware virtuális gép biztonsági mentéséhez

A MABS v3 UR1 a VMware virtuális gépek biztonsági mentésének adott lemezeit is kizárhatja. További információ a [VMWare virtuális gép biztonsági mentésének lemezekről való kizárásáról](backup-azure-backup-server-vmware.md#exclude-disk-from-vmware-vm-backup).  

### <a name="support-for-additional-layer-of-authentication-to-delete-online-backup"></a>További hitelesítési réteg támogatása az online biztonsági mentés törléséhez

A MABS v3 UR1 további hitelesítési réteget adnak hozzá a kritikus fontosságú műveletekhez. A rendszer felszólítja, hogy adjon meg egy biztonsági PIN-kódot, ha az **adatok törlése művelettel leállítja a védelmet** .

### <a name="offline-backup-improvements"></a>Offline biztonsági mentés fejlesztése

A MABS v3 UR1 az Azure import/export szolgáltatással javítja az offline biztonsági mentés élményét. További információkért tekintse meg a frissített lépéseket [itt](./backup-azure-backup-server-import-export.md).

>[!NOTE]
>A frissítés az offline biztonsági mentés előzetes verzióját is megjeleníti Azure Data Box használatával a MABS-ben. További információért forduljon a következőhöz: [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com) .

### <a name="new-cmdlet-parameter"></a>Új parancsmag paraméter

A MABS v3 UR1 tartalmaz egy új paramétert **[-CheckReplicaFragmentation]**. Az új paraméter kiszámítja egy replika töredezettségi arányát, és tartalmazza a **copy-DPMDatasourceReplica** parancsmagot.

### <a name="32-bit-protection-agent-deprecation"></a>32 bites védelmi ügynök elavulása

A MABS v3 UR1 esetében a 32 bites védelmi ügynök támogatása már nem támogatott. A MABS v3-kiszolgáló UR1-re való frissítése után nem fogja tudni biztosítani a 32 bites munkaterhelések elleni védekezést. A meglévő 32 bites védelmi ügynökök letiltott állapotban lesznek, és az ütemezett biztonsági mentések sikertelenek lesznek, ha az **ügynök le van tiltva** . Ha meg szeretné őrizni az ügynökök biztonsági mentési adatait, leállíthatja a védelmet az adat megőrzése beállítással. Ellenkező esetben a védelmi ügynököt el lehet távolítani.

>[!NOTE]
>Tekintse át a [frissített védelmi mátrixot](./backup-mabs-protection-matrix.md) , hogy megismerje a MABS ur 1.-vel való védelemhez támogatott munkaterheléseket.

## <a name="whats-new-in-mabs-v3-rtm"></a>Az MABS v3 RTM újdonságai

A Microsoft Azure Backup Server 3-as verziója (MABS v3) kritikus hibajavításokat, Windows Server 2019-támogatást, SQL 2017-támogatást és egyéb funkciókat és fejlesztéseket tartalmaz. A rögzített hibák listájának és a MABS v3 telepítési utasításának megtekintéséhez lásd a TUDÁSBÁZIS [4457852](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3). számú cikkét.

A MABS v3 a következő funkciókat tartalmazza:

### <a name="volume-to-volume-migration"></a>Kötetről kötetre való Migrálás

A MABS v2 modern biztonsági másolati tárhely (MBS)-ben bejelentettük a számítási feladatok ellátására szolgáló tárhelyet, ahol bizonyos számítási feladatokat úgy konfigurálhat, hogy a tárolási tulajdonságok alapján biztonsági mentést lehessen készíteni egy adott tárhelyre. A konfigurálást követően azonban előfordulhat, hogy bizonyos adatforrások biztonsági másolatait át kell helyeznie más tárolóba az optimalizált erőforrás-használat érdekében. A MABS v3 lehetővé teszi a biztonsági másolatok átkonfigurálását és a különböző köteteken való tárolását [három lépésben](https://techcommunity.microsoft.com/t5/system-center-blog/sc-2016-dpm-ur4-migrate-backup-storage-in-3-simple-steps/ba-p/351842).

### <a name="prevent-unexpected-data-loss"></a>Váratlan adatvesztés megakadályozása

A vállalatokban a MABS-t a rendszergazdák csoportja felügyeli. Noha a biztonsági mentéshez használt tárterületre vonatkozó irányelvek is megtalálhatók, a biztonsági mentési tár MABS miatt helytelen kötet a kritikus adatvesztéshez vezethet. A MABS v3 használatával megakadályozhatja, hogy ezeket a köteteket konfigurálja úgy, hogy [ezeket a PowerShell-parancsmagokat](./backup-mabs-add-storage.md)használó tárolók számára ne legyenek elérhetők.

### <a name="custom-size-allocation"></a>Egyéni méret kiosztása

A modern biztonsági másolati tárhely (MBS) a tárolást vékonyan, és szükség esetén használja. Ehhez a MABS kiszámítja a biztonsági mentésre kerülő adatmennyiséget, amikor a védelemre van konfigurálva. Ha azonban sok fájl és mappa biztonsági mentése folyamatban van, például egy fájlkiszolgáló esetében, a méret kiszámítása hosszú időt is igénybe vehet. A MABS v3 használatával beállíthatja, hogy a MABS az alapértelmezett értékként fogadja el a kötet méretét az egyes fájlok méretének kiszámítása helyett, ami időt takarít meg.

### <a name="optimized-cc-for-rct-vms"></a>RCT virtuális gépekhez optimalizált CC

A MABS a RCT (a natív változások követése a Hyper-V-ben) használja, ami csökkenti az időigényes konzisztencia-ellenőrzés szükségességét a virtuális gépek összeomlása esetén. Az RCT a VSS pillanatkép-alapú biztonsági mentések által biztosított változáskövetésnél nagyobb rugalmasságot biztosít. A MABS v3 a konzisztencia-ellenőrzések során csak a módosult adatmennyiségek átvitelével optimalizálja a hálózat és a tárterület felhasználását.

### <a name="support-to-tls-12"></a>A TLS 1,2 támogatása

A TLS 1,2 a Microsoft által a legjobb osztályú titkosítással javasolt kommunikáció biztonságos módja. A MABS mostantól támogatja a TLS 1,2 kommunikációt a MABS és a védett kiszolgálók között, a tanúsítványalapú hitelesítéshez és a Felhőbeli biztonsági mentésekhez.

### <a name="vmware-vm-protection-support"></a>VMware VM-védelem támogatása

A VMware virtuális gép biztonsági mentése mostantól támogatott az éles környezetben. A MABS v3 a következőt kínálja a VMware virtuális gépek védelméhez:

* A vCenter és ESXi 6,5 támogatását, valamint a 5,5 és 6,0 támogatását.
* VMware virtuális gépek automatikus védelme a felhőben. Ha új VMware virtuális gépeket ad hozzá egy védett mappához, azokat a rendszer automatikusan a lemezre és a felhőbe védi.
* Helyreállítási hatékonyság javítása a VMware alternatív hely helyreállításához.

### <a name="sql-2017-support"></a>SQL 2017-támogatás

A MABS v3 az SQL 2017-mel telepíthető MABS-adatbázisként. Frissítheti az SQL Servert az SQL 2016-ről az SQL 2017-re, vagy telepítheti azt frissen. Az SQL 2017 számítási feladatok biztonsági mentését is elvégezheti fürtözött és nem fürtözött környezetben is a MABS v3 használatával.

### <a name="windows-server-2019-support"></a>Windows Server 2019 támogatás

A MABS v3 telepíthető a Windows Server 2019-es verzióra. A MABS v3 és a WS2019 használatával az operációs rendszer a MABS v3 verzióra való telepítése/frissítése előtt, illetve az operációs rendszer frissítését követően is frissíthető a WS2019-re.

A MABS v3 egy teljes kiadás, amely közvetlenül telepíthető a Windows Server 2016, a Windows Server 2019 vagy a MABS v2 verzióról is. A Backup Server v3 verzióra való frissítés előtt olvassa el a telepítési előfeltételek című részt.
További információ [a MABS telepítési](./backup-azure-microsoft-azure-backup.md#software-package)/frissítési lépéseiről.

> [!NOTE]
>
> A MABS ugyanazzal a kóddal rendelkezik, mint a System Center Data Protection Manager. A MABS v3 egyenértékű a Data Protection Manager 1807-vel. A MABS v3 UR1 egyenértékű a Data Protection Manager 2019 UR1.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan készítheti elő a kiszolgálót, vagy megkezdheti a munkaterhelés védelmét:

* [A biztonsági mentési kiszolgáló munkaterhelésének előkészítése](backup-azure-microsoft-azure-backup.md)
* [VMware-kiszolgáló biztonsági mentése a Backup Server használatával](backup-azure-backup-server-vmware.md)
* [Az SQL Server biztonsági mentése a Backup Server használatával](backup-azure-sql-mabs.md)
* [modern biztonsági másolati tárhely használata a Backup Serverrel](backup-mabs-add-storage.md)
