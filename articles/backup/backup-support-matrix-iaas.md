---
title: Azure-beli virtuális gépek biztonsági mentésének támogatási mátrixa
description: Összefoglalja a támogatási beállításokat és korlátozásokat az Azure-beli virtuális gépek biztonsági Azure Backup használatával.
ms.topic: conceptual
ms.date: 09/13/2019
ms.custom: references_regions
ms.openlocfilehash: 1f63d0c3ad448a8ab9b91764d4c369fefddea25d
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516722"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Azure-beli virtuális gépek biztonsági mentésének támogatási mátrixa

Az Azure Backup [a](backup-overview.md) helyszíni gépek és számítási feladatok, valamint az Azure-beli virtuális gépek (VM-ek) biztonságimentéséhez. Ez a cikk összefoglalja a támogatási beállításokat és korlátozásokat az Azure-beli virtuális gépek biztonsági Azure Backup.

Egyéb támogatási mátrixok:

- [Általános támogatási mátrix a](backup-support-matrix.md) Azure Backup
- [Támogatási mátrix](backup-support-matrix-mabs-dpm.md) a Azure Backup/System Center Data Protection Manager (DPM) biztonsági mentéséhez
- [Támogatási mátrix](backup-support-matrix-mars-agent.md) a Microsoft Azure Recovery Services- (MARS-) ügynökkel való biztonsági mentéshez

## <a name="supported-scenarios"></a>Támogatott esetek

Az Azure-beli virtuális gépek biztonsági mentését és visszaállítását az Azure Backup segítségével.

**Forgatókönyv** | **Biztonsági mentés** | **Ügynök** |**Visszaállítás**
--- | --- | --- | ---
Azure-beli virtuális gépek közvetlen biztonsági mentése  | A teljes virtuális gép biztonsági visszaállítása.  | Az Azure-beli virtuális gépen nincs szükség további ügynökre. Azure Backup telepíti és bővítményt használ a [](../virtual-machines/extensions/agent-windows.md) virtuális gépen futó Azure-beli virtuálisgép-ügynökhöz. | Visszaállítás a következőképpen:<br/><br/> - **Hozzon létre egy alapszintű virtuális gépet.** Ez akkor hasznos, ha a virtuális gép nem rendelkezik speciális konfigurációval, például több IP-címmel.<br/><br/> - **Állítsa vissza a virtuálisgép-lemezt.** Állítsa vissza a lemezt. Ezután csatolja egy meglévő virtuális géphez, vagy hozzon létre egy új virtuális gépet a lemezről a PowerShell használatával.<br/><br/> - **Cserélje le a virtuálisgép-lemezt.** Ha egy virtuális gép létezik, és felügyelt lemezeket használ (titkosítatlan), visszaállíthat egy lemezt, és lecserélheti vele a virtuális gép egy meglévő lemezét.<br/><br/> - **Adott fájlok/mappák visszaállítása.** A fájlokat/mappákat a teljes virtuális gép helyett egy virtuális gépről is visszaállíthatja.
Azure-beli virtuális gépek közvetlen biztonsági mentése (csak Windowson)  | Biztonsági mentése adott fájlokról,mappákról/kötetről. | Telepítse az [Azure Recovery Services-ügynököt.](backup-azure-file-folder-backup-faq.yml)<br/><br/> A MARS-ügynököt futtathatja az Azure-beli virtuálisgép-ügynök biztonsági mentési bővítménye mellett a virtuális gép fájl-/mappaszinten való biztonsági mentéséhez. | Adott mappák/fájlok visszaállítása.
Azure-beli virtuális gép biztonsági mentése biztonsági mentési kiszolgálóra  | Fájlok,mappák/kötetek biztonsági mentése; rendszerállapot/operációs rendszer nélküli fájlok; alkalmazásadatok System Center DPM-hez vagy Microsoft Azure Backup (MABS)-hez.<br/><br/> A DPM/MABS ezután biztonsági mentést készít a Backup-tárolóba. | Telepítse a DPM/MABS védelmi ügynököt a virtuális gépre. A MARS-ügynök telepítve van a DPM/MABS-re.| Fájlok/mappák/kötetek visszaállítása; rendszerállapot/operációs rendszer nélküli fájlok; alkalmazásadatok.

További információ a biztonsági mentési [kiszolgálóval való biztonsági mentésről](backup-architecture.md#architecture-back-up-to-dpmmabs) és a [támogatási követelményekről.](backup-support-matrix-mabs-dpm.md)

## <a name="supported-backup-actions"></a>Támogatott biztonsági mentési műveletek

**Művelet** | **Támogatás**
--- | ---
Virtuális gép biztonsági visszaállítása leállítási/offline állapotú virtuális gépről | Támogatott.<br/><br/> A pillanatkép csak összeomlás-konzisztens, nem pedig alkalmazás-konzisztens.
Lemezek biztonságiának biztonságiolása a felügyelt lemezekre való áttelepítés után | Támogatott.<br/><br/> A biztonsági mentés továbbra is működni fog. Semmit nem kell tenni.
Felügyelt lemezek biztonságiása az erőforráscsoport-zárolás engedélyezése után | Nem támogatott.<br/><br/> Azure Backup a régebbi visszaállítási pontokat, és a biztonsági mentések sikertelenek lesznek, amikor elérik a visszaállítási pontok maximális korlátját.
Virtuális gép biztonsági mentési szabályzatának módosítása | Támogatott.<br/><br/> A virtuális gépről az új szabályzat ütemezési és megőrzési beállításainak használatával lesz biztonságimentés. Ha a megőrzési beállítások meg vannak hosszabbodva, a rendszer megjelöli és megtartja a meglévő helyreállítási pontokat. Ha csökkentik a helyreállítási pontokat, a rendszer a következő tisztítási feladat során törli a meglévő helyreállítási pontokat, és végül törli őket.
Biztonsági mentési feladat megszakítása| A pillanatkép-készít folyamat során támogatott.<br/><br/> A pillanatkép tárolóba való átvitele nem támogatott.
A virtuális gép biztonsági visszaállítása egy másik régióba vagy előfizetésbe |Nem támogatott.<br><br>A sikeres biztonsági mentéshez a virtuális gépeknek ugyanabban az előfizetésben kell lennie, mint a biztonsági mentés tárolója.
Biztonsági másolatok naponta (azure-beli virtuálisgép-bővítményen keresztül) | Naponta egy ütemezett biztonsági mentés.<br/><br/>Az Azure Backup szolgáltatás naponta legfeljebb három igény szerinti biztonsági mentést és egy további ütemezett biztonsági mentést támogat.
Napi biztonsági mentések (MARS-ügynökkel) | Naponta három ütemezett biztonsági mentés.
Biztonsági másolatok naponta (DPM/MABS-en keresztül) | Naponta két ütemezett biztonsági mentés.
Havi/éves biztonsági mentés| Nem támogatott az Azure-beli virtuálisgép-bővítmény használatával való biztonságimentéskor. Csak naponta és hetente támogatott.<br/><br/> Beállíthatja úgy a szabályzatot, hogy a napi/heti biztonsági másolatokat havi/éves megőrzési időtartamra őrizze meg.
Automatikus órakorrekció | Nem támogatott.<br/><br/> Azure Backup nem módosítja automatikusan a nyári időszámítás változásait a virtuális gépek biztonsági mentésekor.<br/><br/>  Módosítsa a szabályzatot manuálisan, ha szükséges.
[Biztonsági funkciók hibrid biztonsági mentéshez](./backup-azure-security-feature.md) |A biztonsági funkciók letiltása nem támogatott.
Annak a virtuális gépnek a biztonsági visszaállítása, amelynek a gépe megváltozott | Nem támogatott.<br/><br/> Ha a gép idejét a virtuális gép biztonsági mentésének engedélyezése után egy jövőbeli dátum-időpontra módosítják, de még ha az idő is visszaáll, a sikeres biztonsági mentés nem garantált.

## <a name="operating-system-support-windows"></a>Operációs rendszer támogatása (Windows)

Az alábbi táblázat összefoglalja a Támogatott operációs rendszereket a Windows Azure-beli virtuális gépek biztonsági telepítésekor.

**Forgatókönyv** | **Operációs rendszer támogatása**
--- | ---
Biztonságimentás az Azure-beli virtuálisgép-ügynök bővítmény használatával | - Windows 10 Client (csak 64 bites) <br/><br/>- Windows Server 2019 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2016 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2012 R2 (Datacenter/Standard) <br/><br/> - Windows Server 2012 (Datacenter/Standard) <br/><br/> - Windows Server 2008 R2 (RTM és SP1 Standard)  <br/><br/> - Windows Server 2008 (csak 64 bites)
Biztonságimentás MARS-ügynökkel | [Támogatott](backup-support-matrix-mars-agent.md#supported-operating-systems) operációs rendszerek.
Biztonságimentés a DPM/MABS-sel | A [MABS](backup-mabs-protection-matrix.md) és a DPM által támogatott operációs rendszerek a [biztonsági mentéshez.](/system-center/dpm/dpm-protection-matrix)

Az Azure Backup nem támogatja a 32 bites operációs rendszereket.

## <a name="support-for-linux-backup"></a>Linuxos biztonsági mentés támogatása

A linuxos gépekről a támogatott rendszerek a támogatottak.

**Művelet** | **Támogatás**
--- | ---
Linux rendszerű Azure-beli virtuális gépek biztonsági visszaállítása a Linux Azure-beli virtuálisgép-ügynökkel | Fájl konzisztens biztonsági mentés.<br/><br/> Alkalmazás-konzisztens biztonsági mentés [egyéni szkriptekkel.](backup-azure-linux-app-consistent.md)<br/><br/> A visszaállítás során létrehozhat egy új virtuális gépet, visszaállíthat egy lemezt, majd felhasználhatja virtuális gép létrehozására, vagy visszaállíthat egy lemezt, és lecserélheti egy meglévő virtuális gép lemezét. Egyes fájlokat és mappákat is visszaállíthat.
Linux rendszerű Azure-beli virtuális gépek biztonságiának biztonságiba való betekintés MARS-ügynökkel | Nem támogatott.<br/><br/> A MARS-ügynök csak Windows rendszerű gépeken telepíthető.
Linuxos Azure-beli virtuális gépek biztonsági telepítése DPM/MABS használatával | Nem támogatott.
Linuxos Azure-beli virtuális gépek biztonsági mentése Docker csatlakoztatási pontokkal | A Azure Backup nem támogatja a Docker csatlakoztatási pontjainak kizárását, mivel ezek minden alkalommal különböző elérési utakhoz vannak csatlakoztatva.

## <a name="operating-system-support-linux"></a>Operációs rendszer támogatása (Linux)

Az Azure-beli virtuális gépek linuxos biztonsági Azure Backup támogatja az Azure által támogatott [Linux-disztribúciók listáját.](../virtual-machines/linux/endorsed-distros.md) Vegye figyelembe a következőket:

- Azure Backup nem támogatja a Core OS Linuxot.
- Az Azure Backup nem támogatja a 32 bites operációs rendszereket.
- Más saját Linux-disztribúciók is működnek, ha a Linuxhoz készült [Azure-beli](../virtual-machines/extensions/agent-linux.md) virtuálisgép-ügynök elérhető a virtuális gépen, és ha a Python támogatott.
- Azure Backup linuxos virtuális gép nem támogatja a proxyval konfigurált virtuális gépet, ha nincs telepítve a Python 2.7-es verziója.
- Azure Backup nem támogatja a tárolóból vagy más NFS-kiszolgálóról Linux vagy Windows rendszerű gépekre csatlakoztatott NFS-fájlok biztonsági mentése. Csak a virtuális géphez helyileg csatolt lemezekről készült biztonsági adatokat.

## <a name="backup-frequency-and-retention"></a>Biztonsági mentés gyakorisága és megőrzése

**Beállítás** | **Korlátok**
--- | ---
Helyreállítási pontok maximális száma védett példányonként (gép/számítási feladat) | 9999.
Helyreállítási pont maximális lejárati ideje | Nincs korlát (99 év).
Tároló biztonsági mentésének maximális gyakorisága (Azure-beli virtuálisgép-bővítmény) | Naponta egyszer.
Tároló biztonsági mentésének maximális gyakorisága (MARS-ügynök) | Naponta három biztonsági másolat.
A DPM/MABS biztonsági mentésének maximális gyakorisága | 15 percenként a SQL Server.<br/><br/> Más számítási feladatok esetében óránként egyszer.
Helyreállítási pont megőrzése | Naponta, hetente, havonta és évente.
Maximális megőrzési időtartam | A biztonsági mentés gyakoriságától függ.
Helyreállítási pontok a DPM-/MABS-lemezen | Fájlkiszolgálók esetén 64, alkalmazáskiszolgálók esetén 448.<br/><br/> A szalagos helyreállítási pontok korlátlanok a helyszíni DPM-hez.

## <a name="supported-restore-methods"></a>Támogatott visszaállítási módszerek

**Visszaállítási lehetőség** | **Részletek**
--- | ---
**Új virtuális gép létrehozása** | Gyorsan létrehoz és elindít egy alapszintű virtuális gépet egy visszaállítási pontból.<br/><br/> Megadhatja a virtuális gép nevét, kiválaszthatja azt az erőforráscsoportot és virtuális hálózatot (VNet), amelybe a virtuális hálózatot helyezni fogja, és megadhat egy tárfiókot a visszaállított virtuális géphez. Az új virtuális gépet a forrás virtuális gépével azonos régióban kell létrehozni.
**Lemez visszaállítása** | Visszaállítja egy virtuális gép lemezét, amely felhasználható egy új virtuális gép létrehozására.<br/><br/> Az Azure Backup biztosít egy sablont, amely segít a virtuális gépek testreszabásában és létrehozásában. <br/><br> A visszaállítási feladat létrehoz egy sablont, amely letölthető és használható egyéni virtuálisgép-beállítások megadásához, valamint virtuális gép létrehozásához.<br/><br/> A lemezek a megadott erőforráscsoportba vannak másolva.<br/><br/> Másik lehetőségként csatlakoztathatja a lemezt egy meglévő virtuális géphez, vagy létrehozhat egy új virtuális gépet a PowerShell használatával.<br/><br/> Ez a lehetőség olyankor hasznos, ha testre szeretné szabni a virtuális gépet, olyan konfigurációs beállításokat szeretne hozzáadni, amelyek a biztonsági másolat készítésekor még nem voltak meg, vagy olyan beállításokat szeretne megadni, amelyek a sablon vagy a PowerShell használatával konfigurálhatók.
**Meglévő cseréje** | Visszaállíthat egy lemezt, és lecserélheti egy meglévő virtuális gépen.<br/><br/> Az aktuális virtuális gépnek léteznie kell. Ha az már törölve lett, ez a lehetőség nem használható.<br/><br/> Azure Backup pillanatképet készít a meglévő virtuális gépről a lemez cseréje előtt, és a megadott előkészítési helyen tárolja azt. A virtuális képhez csatlakoztatott meglévő lemezek a kiválasztott visszaállítási ponttal lesznek felülírva.<br/><br/> A rendszer átmásolja a pillanatképet a tárolóba, és megőrzi a megőrzési szabályzatnak megfelelően. <br/><br/> A lemez cseréje művelet után az eredeti lemez megmarad az erőforráscsoportban. Dönthet úgy, hogy manuálisan törli az eredeti lemezeket, ha nincs rájuk szükség. <br/><br/>A meglévő cseréje a titkosítatlan felügyelt virtuális gépek és az egyéni rendszerképekkel létrehozott virtuális [gépek esetén támogatott.](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/) Nem nem támogatott lemezek és virtuális gépek, klasszikus virtuális gépek és [általánosított](../virtual-machines/windows/capture-image-resource.md)virtuális gépek esetén nem támogatott.<br/><br/> Ha a visszaállítási pont több vagy kevesebb lemezzel rendelkezik, mint az aktuális virtuális gép, akkor a visszaállítási ponton lévő lemezek száma csak a virtuális gép konfigurációját fogja tükrözni.<br><br> A meglévő cseréje a virtuális gépek esetén is támogatott társított erőforrásokkal, például felhasználó által hozzárendelt [felügyelt](../active-directory/managed-identities-azure-resources/overview.md) identitással és [Key Vault.](../key-vault/general/overview.md)
**Régiók közötti (másodlagos régió)** | A régiók közötti visszaállítás az Azure-beli virtuális gépek visszaállítására használható a másodlagos régióban, amely egy [Párosított Azure-régió.](../best-practices-availability-paired-regions.md#what-are-paired-regions)<br><br> A kiválasztott helyreállítási pont összes Azure-beli virtuális gépét visszaállíthatja, ha a biztonsági mentés a másodlagos régióban történik.<br><br> Ez a funkció az alábbi lehetőségekhez érhető el:<br> <li> [Virtuális gép létrehozása](./backup-azure-arm-restore-vms.md#create-a-vm) <br> <li> [Lemezek visszaállítása](./backup-azure-arm-restore-vms.md#restore-disks) <br><br> A Meglévő lemezek cseréje lehetőség jelenleg nem [támogatott.](./backup-azure-arm-restore-vms.md#replace-existing-disks)<br><br> Engedélyek<br> A másodlagos régió visszaállítási műveletét a Biztonsági mentés rendszergazdái és az alkalmazás rendszergazdái hajtják végre.

## <a name="support-for-file-level-restore"></a>Fájlszintű visszaállítás támogatása

**Visszaállítás** | **Támogatott**
--- | ---
Fájlok visszaállítása operációs rendszerek között | A fájlokat bármely olyan gépen visszaállíthatja, amely ugyanazokkal az operációs rendszerekkel (vagy kompatibilisekkel) rendelkezik, mint a biztonsági másolatban lévő virtuális gép. Lásd a [Compatible OS (Kompatibilis operációs rendszer) táblázatot.](backup-azure-restore-files-from-vm.md#step-3-os-requirements-to-successfully-run-the-script)
Fájlok visszaállítása titkosított virtuális gépekről | Nem támogatott.
Fájlok visszaállítása hálózati korlátozott tárfiókból | Nem támogatott.
Fájlok visszaállítása virtuális gépeken a Windows Tárolóhelyek használatával | A visszaállítás nem támogatott ugyanazon a virtuális gépen.<br/><br/> Ehelyett állítsa vissza a fájlokat egy kompatibilis virtuális gépen.
Fájlok visszaállítása Linux rendszerű virtuális gépen LVM-/Raid-tömbök használatával | A visszaállítás nem támogatott ugyanazon a virtuális gépen.<br/><br/> Visszaállítás kompatibilis virtuális gépen.
Speciális hálózati beállításokkal megadott fájlok visszaállítása | A visszaállítás nem támogatott ugyanazon a virtuális gépen. <br/><br/> Visszaállítás kompatibilis virtuális gépen.
Fájlok visszaállítása megosztott lemezről, ideiglenes meghajtóról, deduplikált lemezről, ultralemezről és lemezről, engedélyezett írásgyorsítóval | A visszaállítás nem támogatott, <br/><br/>lásd: [Az Azure-beli virtuális gépek tárterület-támogatása.](#vm-storage-support)

## <a name="support-for-vm-management"></a>Virtuális gépek felügyeletének támogatása

Az alábbi táblázat összefoglalja a biztonsági mentés támogatását a virtuális gépek felügyeleti feladatai során, például a virtuális gépek lemezének hozzáadását vagy cseréjét.

**Visszaállítás** | **Támogatott**
--- | ---
Visszaállítás előfizetés/régió/zóna között. | Nem támogatott.
Visszaállítás meglévő virtuális gépre | Használja a Lemez cseréje lehetőséget.
Lemez visszaállítása az Azure Storage Service Encryption (SSE) számára engedélyezett tárfiókkal | Nem támogatott.<br/><br/> Visszaállítás olyan fiókra, amely nincs engedélyezve az SSE-hez.
Visszaállítás vegyes tárfiókba |Nem támogatott.<br/><br/> A tárfiók típusa alapján az összes visszaállított lemez prémium vagy standard lesz, és nem vegyes.
Virtuális gép visszaállítása közvetlenül egy rendelkezésre állási készletbe | Felügyelt lemezek esetén visszaállíthatja a lemezt, és használhatja a rendelkezésre állási készlet lehetőséget a sablonban.<br/><br/> Nem támogatott a nem támogatott lemezeken. Nem használt lemezek esetén állítsa vissza a lemezt, majd hozzon létre egy virtuális gépet a rendelkezésre állási csoportban.
Nem felügyelt virtuális gépek biztonsági másolatának visszaállítása a felügyelt virtuális gépre való frissítés után| Támogatott.<br/><br/> Visszaállíthatja a lemezeket, majd létrehozhat egy felügyelt virtuális gépet.
Virtuális gép visszaállítása a virtuális gép felügyelt lemezekre való áttelepítése előtti visszaállítási pontra | Támogatott.<br/><br/> A visszaállítást nem felügyelt lemezekre kell visszaállítani (alapértelmezett), a visszaállított lemezeket felügyelt lemezre kell konvertálni, és létre kell hoznia egy virtuális gépet a felügyelt lemezekkel.
Törölt virtuális gép visszaállítása. | Támogatott.<br/><br/> A virtuális gépet helyreállítási pontról is visszaállíthatja.
Tartományvezérlő virtuális gép visszaállítása  | Támogatott. Részletekért lásd: [Tartományvezérlő virtuális gépek visszaállítása.](backup-azure-arm-restore-vms.md#restore-domain-controller-vms)
Virtuális gép visszaállítása másik virtuális hálózatban |Támogatott.<br/><br/> A virtuális hálózatnak ugyanabban az előfizetésben és régióban kell lennie.

## <a name="vm-compute-support"></a>Virtuális gépek számítási támogatása

**Számítás** | **Támogatás**
--- | ---
Virtuális gép mérete |Bármely Azure-beli virtuális gép mérete legalább 2 processzormaggal és 1 GB RAM-mal.<br/><br/> [Részletek](../virtual-machines/sizes.md)
Rendelkezésre állási készletekben álló virtuális [gépek biztonsági biztonságiba való biztonságitatása](../virtual-machines/availability.md#availability-sets) | Támogatott.<br/><br/> Az elérhető készletben található virtuális gépek nem állíthatók vissza a virtuális gépek gyors létrehozásához használható kapcsolóval. Ehelyett a virtuális gép visszaállításakor állítsa vissza a lemezt, és használja egy virtuális gép üzembe helyezéséhez, vagy egy lemez visszaállításához és egy meglévő lemez cseréjéhez.
A [Hybrid Use Benefit (HUB)](../virtual-machines/windows/hybrid-use-benefit-licensing.md) használatával üzembe helyezett virtuális gépek biztonsági telepítése | Támogatott.
A virtuális gépről üzembe helyezett virtuális gépek [biztonsági Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images)<br/><br/> (A Microsoft által közzétett, harmadik féltől származó) |Támogatott.<br/><br/> A virtuális gépnek támogatott operációs rendszert kell futtatónak lennie.<br/><br/> Amikor helyreállítja a fájlokat a virtuális gépen, csak egy kompatibilis operációs rendszerre (nem egy korábbi vagy újabb operációs rendszerre) tud visszaállítani. A virtuális gépek biztonsági Azure Marketplace nem állíthatók vissza, mivel ezekhez vásárlási információra van szükség. Ezek csak lemezként vannak visszaállva.
Egyéni rendszerképből (külső gyártótól) üzembe helyezett virtuális gépek biztonsági létrehozása |Támogatott.<br/><br/> A virtuális gépnek támogatott operációs rendszert kell futtatónak lennie.<br/><br/> Amikor helyreállítja a fájlokat a virtuális gépen, csak egy kompatibilis operációs rendszerre (nem egy korábbi vagy újabb operációs rendszerre) tud visszaállítani.
Az Azure-ba migrált virtuális gépek biztonsági| Támogatott.<br/><br/> A virtuális gép biztonsági telepítésére a virtuálisgép-ügynököt telepíteni kell a migrált gépen.
Több virtuális gép konzisztenciájának biztonsági visszaállítása | Azure Backup nem biztosít adat- és alkalmazáskonzisztenciát több virtuális gép között.
Biztonsági mentés diagnosztikai [beállításokkal](../azure-monitor/essentials/platform-logs-overview.md)  | Támogatott. <br/><br/> Ha az Azure-beli virtuális gép diagnosztikai [](backup-azure-arm-restore-vms.md#create-a-vm) beállításokkal való visszaállítása az Új létrehozása lehetőséggel aktiválódik, akkor a visszaállítás sikertelen lesz.
Zóna által rögzített virtuális gépek visszaállítása | Támogatott (olyan virtuális gépek esetében, amelyekről 2019 januárja után biztonságimentett biztonsági rendszer van, és ahol rendelkezésre [állási zónák érhetők](https://azure.microsoft.com/global-infrastructure/availability-zones/) el).<br/><br/>Jelenleg támogatjuk a virtuális gépeken rögzített zónába való visszaállítást. Ha azonban a zóna egy kimaradás miatt nem érhető el, a visszaállítás sikertelen lesz.
Gen2 virtuális gépek | Támogatott <br> Azure Backup gen2 virtuális gépek biztonsági mentését és [visszaállítását támogatja.](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/) Amikor ezeket a virtuális gépeket visszaállítja a helyreállítási pontról, [a 2. generációs](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/)virtuális gépekként állnak vissza.
Azure-beli virtuális gépek biztonsági mentése zárolásokkal | Nem támogatott nem felügyeletű virtuális gépekhez. <br><br> Felügyelt virtuális gépek esetén támogatott.
[Spot virtuális gépek](../virtual-machines/spot-vms.md) | Támogatott. Azure Backup visszaállítja a spot virtuális gépeket normál Azure-beli virtuális gépekként.
[Azure Dedicated Host](../virtual-machines/dedicated-hosts.md) | Támogatott
Önálló Azure-beli virtuális gépek Windows Storage Spaces-konfigurációja | Támogatott
[Azure VM Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md#scale-sets-with-flexible-orchestration) | Egységes és rugalmas vezénylési modellek esetében is támogatott az egyetlen Azure-beli virtuális gép biztonsági mentésének és visszaállításának támogatása.

## <a name="vm-storage-support"></a>Virtuálisgép-tárolók támogatása

**Összetevő** | **Támogatás**
--- | ---
Azure-beli virtuális gépek adatlemezei | Azure-beli virtuális gépek legfeljebb 32 lemezzel való biztonsági mentésének támogatása.<br><br> Az Azure-beli virtuális gépek nem felügyeleti lemezekkel vagy klasszikus virtuális gépekkel való biztonsági mentésének támogatása legfeljebb 16 lemez lehet.
Adatlemez mérete | Egy virtuális gép minden lemeze legfeljebb 32 TB és legfeljebb 256 TB méretű lehet.
Tárolási típus | standard HDD, standard SSD, prémium SSD.
Felügyelt lemezek | Támogatott.
Titkosított lemezek | Támogatott.<br/><br/> A virtuális gépekkel Azure Disk Encryption azure-beli virtuális gépekről (az Azure AD-alkalmazással vagy anélkül) is biztonsági lehet.<br/><br/> A titkosított virtuális gépek fájl-/mappaszinten nem állíthatók helyre. A teljes virtuális gépet helyre kell állítania.<br/><br/> Engedélyezheti a titkosítást olyan virtuális gépeken, amelyek már védve vannak Azure Backup.
Engedélyezett írásgyorsító lemezek | 2020. november 23-án csak Dél-Korea középső régiójában (KRC) és Dél-Afrika északi régiójában (SAN) támogatott korlátozott számú előfizetés esetén. Ezekben a támogatott előfizetések Azure Backup biztonsági másolatot készít a biztonsági mentés során írásgyorsítással (WA) engedélyezett lemezekkel rendelkező virtuális gépekről.<br><br>A nem támogatott régiók esetében internetkapcsolatra van szükség a virtuális gépen, hogy pillanatképeket készítsünk Virtual Machines WA engedélyezése esetén.<br><br> **Fontos megjegyzés:** A WA-lemezekkel nem támogatott régiókban a virtuális gépeknek internetkapcsolatra van szükségük a sikeres biztonsági mentéshez (annak ellenére, hogy ezek a lemezek ki vannak zárva a biztonsági mentésből).
Deduplikált virtuális &/lemezek visszaállítása biztonsági mentésre | Azure Backup nem támogatja a deduplikációt. További információt ebben a cikkben [talál.](./backup-support-matrix.md#disk-deduplication-support) <br/> <br/>  – Azure Backup nem deduplikál a Recovery Services-tárolóban lévő virtuális gépek között <br/> <br/>  – Ha a visszaállítás során vannak deduplikációt végző virtuális gépek, a fájlok nem állíthatók vissza, mert a tároló nem érti a formátumot. A virtuális gép teljes visszaállítását azonban sikeresen végrehajthatja.
Lemez hozzáadása védett virtuális géphez | Támogatott.
Lemez átméretezése védett virtuális gépen | Támogatott.
Megosztott tároló| A virtuális gépek biztonsági mentése Fürt megosztott kötete (CSV) vagy Scale-Out fájlkiszolgáló használatával nem támogatott. A CSV-írók a biztonsági mentés során valószínűleg sikertelenek lesznek. Visszaállítás esetén előfordulhat, hogy a CSV-köteteket tartalmazó lemezek nem jönek elő.
[Megosztott lemezek](../virtual-machines/disks-shared-enable.md) | Nem támogatott.
Ultra SSD-lemezek | Nem támogatott. További információért tekintse meg ezeket a [korlátozásokat.](selective-disk-backup-restore.md#limitations)
[Ideiglenes lemezek](../virtual-machines/managed-disks-overview.md#temporary-disk) | Az ideiglenes lemezekről a rendszer nem biztonsági Azure Backup.
NVMe/élettartamú lemezek | Nem támogatott.

## <a name="vm-network-support"></a>Virtuálisgép-hálózat támogatása

**Összetevő** | **Támogatás**
--- | ---
Hálózati adapterek (HÁLÓZATI adapterek) száma | Egy adott Azure-beli virtuális gép méretében legfeljebb a támogatott hálózati eszközök maximális száma.<br/><br/> A rendszer akkor hoz létre NIC-ket, amikor a virtuális gép létrejön a visszaállítási folyamat során.<br/><br/> A visszaállított virtuális gépen található NIC-k száma tükrözi a virtuális gépen található NIC-k számát, amikor engedélyezte a védelmet. A hálózati számítógépek a védelem engedélyezése után való eltávolítása nincs hatással a számra.
Külső/belső terheléselosztás |Támogatott. <br/><br/> [További információ](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) a speciális hálózati beállításokkal megadott virtuális gépek visszaállításáról.
Több fenntartott IP-cím |Támogatott. <br/><br/> [További információ](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) a speciális hálózati beállításokkal megadott virtuális gépek visszaállításáról.
Több hálózati adapterrel rendelkező virtuális gépek| Támogatott. <br/><br/> [További információ](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) a speciális hálózati beállításokkal megadott virtuális gépek visszaállításáról.
Nyilvános IP-címmel és virtuális gépekkel| Támogatott.<br/><br/> Meglévő nyilvános IP-címet társít a hálózati adapterhez, vagy a visszaállítás után hozzon létre egy címet, és társítsa azt a hálózati adapterhez.
Hálózati biztonsági csoport (NSG) hálózati adapteren/alhálózaton. |Támogatott.
Statikus IP-cím | Nem támogatott.<br/><br/> A visszaállítási pontból létrehozott új virtuális géphez dinamikus IP-cím van rendelve.<br/><br/> Klasszikus virtuális gépek esetén nem lehet fenntartott IP-címmel és meghatározott végponttal nem biztonságimentéseket tartani a virtuális gépekről.
Dinamikus IP-cím |Támogatott.<br/><br/> Ha a forrás virtuális gép hálózati adaptere dinamikus IP-címzést használ, a visszaállított virtuális gép hálózati adaptere alapértelmezés szerint azt is használni fogja.
Azure Traffic Manager| Támogatott.<br/><br/>Ha a biztonsági másolatba biztonsági másolatba Traffic Manager, adja hozzá manuálisan a visszaállított virtuális gépet ugyan Traffic Manager példányhoz.
Azure DNS |Támogatott.
Egyéni DNS |Támogatott.
Kimenő kapcsolat HTTP-proxyn keresztül | Támogatott.<br/><br/> A hitelesített proxyk nem támogatottak.
Virtuális hálózati szolgáltatásvégpontok| Támogatott.<br/><br/> A tűzfal- és virtuális hálózati tárfiók beállításainak minden hálózatról engedélyezniük kell a hozzáférést.

## <a name="vm-security-and-encryption-support"></a>Virtuális gépek biztonságának és titkosításának támogatása

Azure Backup az átvitel közbeni és az in-rest adatok titkosítását:

Hálózati forgalom az Azure-ba:

- A kiszolgálókról a Recovery Services-tárolóba származó biztonsági mentési forgalmat a 256-os Advanced Encryption Standard titkosítja.
- A biztonsági mentési adatokat biztonságos HTTPS-kapcsolaton keresztül küldi el a rendszer.
- A biztonsági mentési adatokat a rendszer titkosított formában tárolja a Recovery Services-tárolóban.
- Csak Ön tudja feloldani az adatokat a titkosítási kulccsal. A Microsoft semmilyen ponton nem tudja visszafejteni a biztonsági mentési adatokat.

  > [!WARNING]
  > A tároló beállítása után csak Ön férhet hozzá a titkosítási kulcshoz. A Microsoft soha nem tart fenn másolatot, és nem fér hozzá a kulcshoz. Ha a kulcs nem megfelelő, a Microsoft nem tudja helyreállítani a biztonsági mentési adatokat.

Az adatbiztonságot:

- Az Azure-beli virtuális gépek biztonsági létrehozása során be kell állítania a titkosítást *a* virtuális gépen belül.
- Azure Backup támogatja a Azure Disk Encryption, amely Windows rendszerű virtuális gépeken BitLockert, Linux rendszerű virtuális gépeken pedig us **dm-cryptet** használ.
- Az Azure Backup a háttérben az [Azure Storage Service Encryption](../storage/common/storage-service-encryption.md) használatával biztosít védelmet az inaktív adatok számára.

**Gép** | **Átvitel közben** | **At rest**
--- | --- | ---
Helyszíni Windows-gépek DPM/MABS nélkül | ![Igen][green] | ![Igen][green]
Azure-beli virtuális gépek | ![Igen][green] | ![Igen][green]
Helyszíni/Azure-beli virtuális gépek DPM-fel | ![Igen][green] | ![Igen][green]
Helyszíni/Azure-beli virtuális gépek MABS-sel | ![Igen][green] | ![Igen][green]

## <a name="vm-compression-support"></a>Virtuális gépek tömörítésének támogatása

A biztonsági mentés támogatja a biztonsági mentési forgalom tömörítését az alábbi táblázatban összefoglalva. Vegye figyelembe a következőket:

- Azure-beli virtuális gépeken a virtuálisgép-bővítmény közvetlenül az Azure Storage-fiókból olvassa be az adatokat a tárolóhálózaton keresztül. Ezt a forgalmat nem szükséges tömöríteni.
- Ha DPM-et vagy MABS-t használ, sávszélességet takaríthat meg, ha tömöríti az adatokat, mielőtt biztonsági mentést adna a DPM/MABS-be.

**Gép** | **Tömörítés MABS-be/DPM-be (TCP)** | **Tömörítés tárolóba (HTTPS)**
--- | --- | ---
Helyszíni Windows-gépek DPM/MABS nélkül | NA | ![Igen][green]
Azure-beli virtuális gépek | NA | NA
Helyszíni/Azure-beli virtuális gépek DPM-fel | ![Igen][green] | ![Igen][green]
Helyszíni/Azure-beli virtuális gépek MABS-sel | ![Igen][green] | ![Igen][green]

## <a name="next-steps"></a>Következő lépések

- [Azure-beli virtuális gépek biztonságimentés.](backup-azure-arm-vms-prepare.md)
- [Windows rendszerű gépek biztonsági mentése közvetlenül ,](tutorial-backup-windows-server-to-azure.md)biztonsági mentési kiszolgáló nélkül.
- [Állítsa be a MABS-t](backup-azure-microsoft-azure-backup.md) az Azure-ba való biztonsági mentéshez, majd a számítási feladatok biztonsági mentését a MABS-be.
- [Állítsa be a DPM-et](backup-azure-dpm-introduction.md) az Azure-ba való biztonsági mentéshez, majd a számítási feladatok biztonsági mentését a DPM-be.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png