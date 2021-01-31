---
title: Azure-beli virtuális gépek biztonsági mentésének támogatási mátrixa
description: Összefoglalja az Azure-beli virtuális gépek Azure Backup szolgáltatással történő biztonsági mentésével kapcsolatos támogatási beállításokat és korlátozásokat.
ms.topic: conceptual
ms.date: 09/13/2019
ms.custom: references_regions
ms.openlocfilehash: 3d1238bffd785a47b6e0ffab70366a15dfff0ef3
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218111"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Azure-beli virtuális gépek biztonsági mentésének támogatási mátrixa

A [Azure Backup szolgáltatás](backup-overview.md) segítségével biztonsági mentést készíthet a helyszíni gépekről és munkaterhelésekről, valamint az Azure-beli virtuális gépekről. Ez a cikk az Azure-beli virtuális gépek Azure Backup-vel történő biztonsági mentésével kapcsolatos támogatási beállításokat és korlátozásokat foglalja össze.

Egyéb támogatási mátrixok:

- [Általános támogatási mátrix](backup-support-matrix.md) a Azure Backuphoz
- [Támogatási mátrix](backup-support-matrix-mabs-dpm.md) Azure Backup Server/System Center Data Protection Manager (DPM) biztonsági mentéshez
- [Támogatási mátrix](backup-support-matrix-mars-agent.md) a Microsoft Azure Recovery Services-(MARS-) ügynökkel való biztonsági mentéshez

## <a name="supported-scenarios"></a>Támogatott esetek

Az Azure-beli virtuális gépek biztonsági mentését és visszaállítását a Azure Backup szolgáltatással végezheti el.

**Forgatókönyv** | **Biztonsági mentés** | **Ügynök** |**Visszaállítás**
--- | --- | --- | ---
Azure-beli virtuális gépek közvetlen biztonsági mentése  | A teljes virtuális gép biztonsági mentése.  | Nincs szükség további ügynökre az Azure-beli virtuális gépen. Azure Backup telepíti és egy bővítményt használ a virtuális gépen futó Azure virtuálisgép- [ügynökhöz](../virtual-machines/extensions/agent-windows.md) . | Visszaállítás a következőképpen:<br/><br/> - **Hozzon létre egy alapszintű virtuális gépet**. Ez akkor hasznos, ha a virtuális gépnek nincs olyan speciális konfigurációja, mint például több IP-cím.<br/><br/> - **Állítsa vissza a virtuális gép lemezét**. Állítsa vissza a lemezt. Ezután csatolja egy meglévő virtuális géphez, vagy hozzon létre egy új virtuális gépet a PowerShell használatával.<br/><br/> - **Cserélje le a virtuális gép lemezét**. Ha egy virtuális gép létezik, és felügyelt lemezeket használ (titkosítatlan), akkor visszaállíthat egy lemezt, és felhasználhatja egy meglévő lemez lecserélésére a virtuális gépen.<br/><br/> - **Adott fájlok vagy mappák visszaállítása**. A virtuális gépek fájljait és mappáit a teljes virtuális gépről nem lehet visszaállítani.
Azure-beli virtuális gépek közvetlen biztonsági mentése (csak Windows)  | Biztonsági másolat készítése adott fájlokról/mappákról/kötetről. | Telepítse az [Azure Recovery Services Agent ügynököt](backup-azure-file-folder-backup-faq.md).<br/><br/> A MARS-ügynököt az Azure virtuálisgép-ügynök biztonsági mentési bővítményével együtt futtathatja a virtuális gép biztonsági mentéséhez a fájl/mappa szintjén. | Adott mappák vagy fájlok visszaállítása.
Azure-beli virtuális gép biztonsági mentése a Backup Serverbe  | Fájlok/mappák/kötetek biztonsági mentése; rendszerállapot/operációs rendszer nélküli fájlok; alkalmazásadatok a System Center DPM vagy a Microsoft Azure Backup kiszolgálóra (MABS).<br/><br/> A DPM/MABS ezután biztonsági mentést készít a Backup-tárolóba. | Telepítse a DPM/MABS védelmi ügynököt a virtuális gépre. A MARS-ügynök telepítve van a DPM/MABS.| Fájlok/mappák/kötetek visszaállítása; rendszerállapot/operációs rendszer nélküli fájlok; alkalmazásadatok.

További információ a biztonsági mentésről [a Backup Server használatával](backup-architecture.md#architecture-back-up-to-dpmmabs) és a [támogatási követelményekkel](backup-support-matrix-mabs-dpm.md)kapcsolatban.

## <a name="supported-backup-actions"></a>Támogatott biztonsági mentési műveletek

**Művelet** | **Támogatás**
--- | ---
Leállítás/offline virtuális gép biztonsági mentése | Támogatott.<br/><br/> A pillanatkép csak Crash-konzisztens, nem az alkalmazás-konzisztens.
Lemezek biztonsági mentése a felügyelt lemezekre való Migrálás után | Támogatott.<br/><br/> A biztonsági mentés továbbra is működni fog. Semmit nem kell tenni.
Felügyelt lemezek biztonsági mentése az erőforráscsoport zárolásának engedélyezése után | Nem támogatott.<br/><br/> Azure Backup nem tudja törölni a régebbi visszaállítási pontokat, és a biztonsági mentések sikertelenek lesznek, ha elérik a visszaállítási pontok maximális korlátját.
Virtuális gép biztonsági mentési szabályzatának módosítása | Támogatott.<br/><br/> A virtuális gép biztonsági mentése az új házirend ütemterv és adatmegőrzési beállítások használatával történik. Ha a megőrzési beállítások meg vannak hosszabbítva, a meglévő helyreállítási pontok meg vannak jelölve és megmaradnak. Ha csökkennek, a rendszer a meglévő helyreállítási pontokat metszi a következő karbantartási feladatokban, és végül törölve lesz.
Biztonsági mentési feladat megszakítása| A pillanatkép-készítési folyamat során támogatott.<br/><br/> Nem támogatott, ha a pillanatképet a tárolóba helyezi át.
A virtuális gép biztonsági mentése egy másik régióba vagy előfizetésbe |Nem támogatott.<br><br>A sikeres biztonsági mentéshez a virtuális gépeknek ugyanahhoz az előfizetéshez kell tartoznia, mint a biztonsági mentéshez.
Biztonsági mentések naponta (az Azure virtuálisgép-bővítmény használatával) | Naponta egy ütemezett biztonsági mentés.<br/><br/>A Azure Backup szolgáltatás naponta akár kilenc igény szerinti biztonsági mentést is támogat, de a Microsoft legfeljebb négy napi igény szerinti biztonsági mentést javasol a legjobb teljesítmény érdekében.
Biztonsági mentések naponta (a MARS-ügynökön keresztül) | Naponta három ütemezett biztonsági mentés.
Biztonsági mentések naponta (DPM/MABS-n keresztül) | Naponta két ütemezett biztonsági mentés.
Havi/éves biztonsági mentés| Nem támogatott az Azure virtuálisgép-bővítménysel történő biztonsági mentés során. Csak naponta és hetente támogatott.<br/><br/> Beállíthatja a szabályzatot, hogy megőrizze a napi/heti biztonsági mentéseket a havi/éves megőrzési időszakra.
Automatikus órajel-beállítás | Nem támogatott.<br/><br/> A Azure Backup nem módosítja automatikusan a nyári időmegtakarítást a virtuális gép biztonsági mentésekor.<br/><br/>  Szükség szerint módosítsa manuálisan a szabályzatot.
[Biztonsági funkciók a hibrid biztonsági mentéshez](./backup-azure-security-feature.md) |A biztonsági funkciók letiltása nem támogatott.
Biztonsági másolat készítése arra a virtuális gépre, amelynek a gépi ideje módosult | Nem támogatott.<br/><br/> Ha a gép ideje a virtuális gép biztonsági mentésének engedélyezése után egy későbbi dátumra változik, akkor is, ha az idő változása visszaállt, a sikeres biztonsági mentés nem garantált.

## <a name="operating-system-support-windows"></a>Operációs rendszer támogatása (Windows)

A következő táblázat összefoglalja a Windows Azure virtuális gépek biztonsági mentése során támogatott operációs rendszereket.

**Forgatókönyv** | **Operációs rendszer támogatása**
--- | ---
Biztonsági mentés az Azure VM Agent bővítménnyel | – Windows 10-ügyfél (csak 64 bites) <br/><br/>– Windows Server 2019 (Datacenter/Datacenter Core/standard) <br/><br/> – Windows Server 2016 (Datacenter/Datacenter Core/standard) <br/><br/> – Windows Server 2012 R2 (Datacenter/standard) <br/><br/> – Windows Server 2012 (Datacenter/standard) <br/><br/> – Windows Server 2008 R2 (RTM és SP1 standard)  <br/><br/> – Windows Server 2008 (csak 64 bites)
Biztonsági mentés a MARS-ügynökkel | [Támogatott](backup-support-matrix-mars-agent.md#supported-operating-systems) operációs rendszerek.
Biztonsági mentés a DPM/MABS | A [MABS](backup-mabs-protection-matrix.md) és [DPM](/system-center/dpm/dpm-protection-matrix)biztonsági mentéshez támogatott operációs rendszerek.

Az Azure Backup nem támogatja a 32 bites operációs rendszereket.

## <a name="support-for-linux-backup"></a>Linux biztonsági mentés támogatása

A Linux rendszerű gépek biztonsági mentését a következők támogatják.

**Művelet** | **Támogatás**
--- | ---
Linux Azure-beli virtuális gépek biztonsági mentése a Linux Azure virtuálisgép-ügynökkel | Fájl konzisztens biztonsági mentése.<br/><br/> Alkalmazás-konzisztens biztonsági mentés [egyéni parancsfájlok](backup-azure-linux-app-consistent.md)használatával.<br/><br/> A visszaállítás során létrehozhat egy új virtuális gépet, visszaállíthat egy lemezt, és felhasználhatja egy virtuális gép létrehozásához, illetve egy lemez visszaállításához, illetve a meglévő virtuális gép lemezének lecseréléséhez. Az egyes fájlokat és mappákat is visszaállíthatja.
Linux Azure-beli virtuális gépek biztonsági mentése a MARS-ügynökkel | Nem támogatott.<br/><br/> A MARS-ügynök csak Windows rendszerű gépekre telepíthető.
Linuxos Azure-beli virtuális gépek biztonsági mentése a DPM/MABS | Nem támogatott.

## <a name="operating-system-support-linux"></a>Operációs rendszer támogatása (Linux)

Az Azure-beli virtuális gépek linuxos biztonsági mentését Azure Backup támogatja az [Azure által támogatott Linux-disztribúciók](../virtual-machines/linux/endorsed-distros.md)listáját. Vegye figyelembe a következőket:

- A Azure Backup nem támogatja a Core OS Linux rendszert.
- Az Azure Backup nem támogatja a 32 bites operációs rendszereket.
- Más, saját linuxos disztribúciók is működhetnek, ha a Linux rendszerhez készült [Azure VM-ügynök](../virtual-machines/extensions/agent-linux.md) elérhető a virtuális gépen, és a Python is támogatott.
- A Azure Backup nem támogatja a proxyval konfigurált linuxos virtuális gépeket, ha nincs telepítve a Python 2,7-es verziója.
- A Azure Backup nem támogatja a Storage-ból vagy más NFS-kiszolgálókról a Linux vagy Windows rendszerű gépekre csatlakoztatott NFS-fájlok biztonsági mentését. Csak azokat a lemezeket támogatja, amelyek helyileg vannak csatlakoztatva a virtuális géphez.

## <a name="backup-frequency-and-retention"></a>Biztonsági mentés gyakorisága és megőrzése

**Beállítás** | **Korlátok**
--- | ---
Helyreállítási pontok maximális száma védett példányon (gép/munkaterhelés) | 9999.
Helyreállítási pont maximális lejárati ideje | Nincs korlát (99 év).
Maximális biztonsági mentési gyakoriság a tárolóhoz (Azure VM-bővítmény) | Naponta egyszer.
Maximális biztonsági mentési gyakoriság a tárolóhoz (MARS-ügynök) | Naponta három biztonsági mentés.
Maximális biztonsági mentés gyakorisága DPM/MABS | SQL Server minden 15 percenként.<br/><br/> Óránként egyszer más munkaterhelések esetében.
Helyreállítási pont megőrzése | Naponta, hetente, havonta és évente.
Maximális megőrzési időtartam | A biztonsági mentés gyakoriságának függvénye.
Helyreállítási pontok a DPM-vagy MABS-lemezen | 64 fájlkiszolgálók és 448 for app Servers.<br/><br/> A szalagos helyreállítási pontok korlátlanok a helyszíni DPM.

## <a name="supported-restore-methods"></a>Támogatott visszaállítási módszerek

**Visszaállítási lehetőség** | **Részletek**
--- | ---
**Új virtuális gép létrehozása** | Gyorsan létrehoz és elindít egy alapszintű virtuális gépet egy visszaállítási pontból.<br/><br/> Megadhatja a virtuális gép nevét, kiválaszthatja azt az erőforráscsoportot és virtuális hálózatot (VNet), amelyben a rendszer elhelyezi, majd megadhat egy Storage-fiókot a visszaállított virtuális géphez. Az új virtuális gépet a forrás virtuális gépével azonos régióban kell létrehozni.
**Lemez visszaállítása** | Visszaállítja egy virtuális gép lemezét, amely felhasználható egy új virtuális gép létrehozására.<br/><br/> Az Azure Backup biztosít egy sablont, amely segít a virtuális gépek testreszabásában és létrehozásában. <br/><br> A visszaállítási feladattípus létrehoz egy sablont, amelyet letöltheti, és használhatja az egyéni virtuálisgép-beállítások megadására és a virtuális gép létrehozásához.<br/><br/> A lemezek a megadott erőforráscsoportba vannak másolva.<br/><br/> Azt is megteheti, hogy csatlakoztatja a lemezt egy meglévő virtuális géphez, vagy létrehozhat egy új virtuális gépet a PowerShell használatával.<br/><br/> Ez a lehetőség olyankor hasznos, ha testre szeretné szabni a virtuális gépet, olyan konfigurációs beállításokat szeretne hozzáadni, amelyek a biztonsági másolat készítésekor még nem voltak meg, vagy olyan beállításokat szeretne megadni, amelyek a sablon vagy a PowerShell használatával konfigurálhatók.
**Meglévő cseréje** | Visszaállíthat egy lemezt, és lecserélheti a meglévő virtuális gép lemezét.<br/><br/> Az aktuális virtuális gépnek léteznie kell. Ha az már törölve lett, ez a lehetőség nem használható.<br/><br/> Azure Backup a lemez cseréje előtt pillanatképet készít a meglévő virtuális gépről, és a megadott átmeneti helyen tárolja azt. A virtuális képhez csatlakoztatott meglévő lemezek a kiválasztott visszaállítási ponttal lesznek felülírva.<br/><br/> A rendszer átmásolja a pillanatképet a tárolóba, és megőrzi a megőrzési szabályzatnak megfelelően. <br/><br/> A lemez cseréje művelet után az eredeti lemez megmarad az erőforráscsoporthoz. Dönthet úgy is, hogy manuálisan törli az eredeti lemezeket, ha azok nem szükségesek. <br/><br/>A meglévő cseréje a nem titkosított felügyelt virtuális gépek és az [Egyéni lemezképek használatával létrehozott](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/)virtuális gépek esetében támogatott. Nem felügyelt lemezek és [általánosított virtuális gépek](../virtual-machines/windows/capture-image-resource.md)esetén nem támogatott.<br/><br/> Ha a visszaállítási pont több vagy kevesebb lemezzel rendelkezik, mint az aktuális virtuális gép, akkor a visszaállítási pontban lévő lemezek száma csak a virtuális gép konfigurációját fogja tükrözni.<br><br> A meglévőt lecserélve a társított erőforrásokkal rendelkező virtuális gépek, például a [felhasználó által hozzárendelt felügyelt identitás](../active-directory/managed-identities-azure-resources/overview.md) és a [Key Vault](../key-vault/general/overview.md)is támogatott.
**Régiók közötti (másodlagos régió)** | A régiók közötti visszaállítással visszaállíthatja az Azure-beli virtuális gépeket a másodlagos régióban, amely egy [Azure párosított régió](../best-practices-availability-paired-regions.md#what-are-paired-regions).<br><br> A kijelölt helyreállítási ponthoz tartozó összes Azure-beli virtuális gépet visszaállíthatja, ha a biztonsági mentést a másodlagos régióban hajtja végre.<br><br> Ez a funkció az alábbi lehetőségekhez érhető el:<br> <li> [Virtuális gép létrehozása](./backup-azure-arm-restore-vms.md#create-a-vm) <br> <li> [Lemezek visszaállítása](./backup-azure-arm-restore-vms.md#restore-disks) <br><br> Jelenleg nem támogatott a [meglévő lemezek cseréje](./backup-azure-arm-restore-vms.md#replace-existing-disks) lehetőség.<br><br> Engedélyek<br> A másodlagos régió visszaállítási művelete a biztonsági mentési rendszergazdák és az alkalmazás-rendszergazdák által végezhető el.

## <a name="support-for-file-level-restore"></a>A fájl szintű visszaállítás támogatása

**Visszaállítás** | **Támogatott**
--- | ---
Fájlok visszaállítása operációs rendszerek között | A fájlokat bármely olyan gépen visszaállíthatja, amelynek a biztonsági másolata azonos (vagy kompatibilis) operációs rendszert futtat. Tekintse meg a [kompatibilis operációsrendszer-táblázatot](backup-azure-restore-files-from-vm.md#step-3-os-requirements-to-successfully-run-the-script).
Fájlok visszaállítása titkosított virtuális gépekről | Nem támogatott.
Fájlok visszaállítása a hálózatra korlátozott tárolási fiókokból | Nem támogatott.
A virtuális gépek fájljainak visszaállítása Windows-tárolóhelyek használatával | A visszaállítás nem támogatott ugyanazon a virtuális gépen.<br/><br/> Ehelyett egy kompatibilis virtuális gépen állítsa vissza a fájlokat.
Linux virtuális gépen lévő fájlok visszaállítása az LVM/RAID tömbök használatával | A visszaállítás nem támogatott ugyanazon a virtuális gépen.<br/><br/> Visszaállítás egy kompatibilis virtuális gépen.
Fájlok visszaállítása speciális hálózati beállításokkal | A visszaállítás nem támogatott ugyanazon a virtuális gépen. <br/><br/> Visszaállítás egy kompatibilis virtuális gépen.

## <a name="support-for-vm-management"></a>A virtuális gépek felügyeletének támogatása

A következő táblázat összefoglalja a virtuális gépek felügyeleti feladatai során történő biztonsági mentés támogatását, például a virtuálisgép-lemezek hozzáadását vagy cseréjét.

**Visszaállítás** | **Támogatott**
--- | ---
Visszaállítás az előfizetés/régió/zóna között. | Nem támogatott.
Visszaállítás meglévő virtuális gépre | Használja a lemez cseréje lehetőséget.
Lemez visszaállítása az Azure Storage Service Encryption (SSE) számára engedélyezett Storage-fiókkal | Nem támogatott.<br/><br/> Állítsa vissza olyan fiókra, amely nem rendelkezik SSE-támogatással.
Visszaállítás vegyes Storage-fiókokba |Nem támogatott.<br/><br/> A Storage-fiók típusa alapján az összes visszaállított lemez prémium vagy standard szintű lesz, és nem vegyes.
Virtuális gép visszaállítása közvetlenül a rendelkezésre állási csoportba | Felügyelt lemezek esetén visszaállíthatja a lemezt, és használhatja a sablon rendelkezésre állási csoportját.<br/><br/> Nem felügyelt lemezek esetén nem támogatott. Nem felügyelt lemezek esetén állítsa vissza a lemezt, majd hozzon létre egy virtuális gépet a rendelkezésre állási csoporton belül.
Nem felügyelt virtuális gépek biztonsági másolatának visszaállítása a felügyelt virtuális gépre való frissítés után| Támogatott.<br/><br/> Visszaállíthatja a lemezeket, majd létrehozhatja a felügyelt virtuális gépet.
Virtuális gép visszaállítása a visszaállítási pontra a virtuális gép felügyelt lemezekre való migrálása előtt | Támogatott.<br/><br/> Visszaállítja a nem felügyelt lemezeket (alapértelmezés), átalakítja a visszaállított lemezeket a felügyelt lemezekre, és létrehoz egy virtuális gépet a felügyelt lemezekkel.
Egy törölt virtuális gép visszaállítása. | Támogatott.<br/><br/> A virtuális gépet helyreállítási pontról állíthatja vissza.
Tartományvezérlő virtuális gép visszaállítása  | Támogatott. További részletek: [tartományvezérlő virtuális gépek visszaállítása](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).
Virtuális gép visszaállítása eltérő virtuális hálózatban |Támogatott.<br/><br/> A virtuális hálózatnak ugyanabban az előfizetésben és régióban kell lennie.

## <a name="vm-compute-support"></a>VIRTUÁLIS gépek számítási támogatása

**Számítás** | **Támogatás**
--- | ---
Virtuális gép mérete |Bármely Azure-beli virtuálisgép-méret legalább 2 CPU-maggal és 1 GB RAM-mal.<br/><br/> [Részletek](../virtual-machines/sizes.md)
Virtuális gépek biztonsági mentése a [rendelkezésre állási csoportokban](../virtual-machines/availability.md#availability-sets) | Támogatott.<br/><br/> Az elérhető készletekben lévő virtuális gépeket nem állíthatja helyre a virtuális gép gyors létrehozásához szükséges lehetőség használatával. Ehelyett a virtuális gép visszaállításakor állítsa vissza a lemezt, és használja egy virtuális gép üzembe helyezéséhez, vagy egy lemez visszaállításához, és használja egy meglévő lemez cseréjéhez.
A [Hybrid use Benefit (hub) használatával](../virtual-machines/windows/hybrid-use-benefit-licensing.md) üzembe helyezett virtuális gépek biztonsági mentése | Támogatott.
Az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images) -ről üzembe helyezett virtuális gépek biztonsági mentése<br/><br/> (Közzétette: Microsoft, harmadik fél) |Támogatott.<br/><br/> A virtuális gépnek támogatott operációs rendszernek kell futnia.<br/><br/> A virtuális gépen lévő fájlok helyreállításakor csak kompatibilis operációs rendszerre (nem egy korábbi vagy újabb operációs rendszerre) lehet visszaállítani. Nem állítunk vissza virtuális gépekként működő Azure piactér-beli virtuális gépeket, mivel ezek vásárlási információkra van szükségük. Csak lemezként vannak visszaállítva.
Egyéni rendszerképből (külső féltől) üzembe helyezett virtuális gépek biztonsági mentése |Támogatott.<br/><br/> A virtuális gépnek támogatott operációs rendszernek kell futnia.<br/><br/> A virtuális gépen lévő fájlok helyreállításakor csak kompatibilis operációs rendszerre (nem egy korábbi vagy újabb operációs rendszerre) lehet visszaállítani.
Az Azure-ba migrált virtuális gépek biztonsági mentése| Támogatott.<br/><br/> A virtuális gép biztonsági mentéséhez telepíteni kell a virtuálisgép-ügynököt az áttelepített gépre.
Több virtuális gépre kiterjedő konzisztencia biztonsági mentése | Azure Backup nem biztosít több virtuális gép között az adatés az alkalmazások konzisztenciáját.
Biztonsági mentés [diagnosztikai beállításokkal](../azure-monitor/platform/platform-logs-overview.md)  | Támogatott. <br/><br/> Ha a diagnosztikai beállításokkal rendelkező Azure-beli virtuális gép visszaállítása [új](backup-azure-arm-restore-vms.md#create-a-vm) lehetőség használatával aktiválódik, a visszaállítás sikertelen lesz.
A zóna által rögzített virtuális gépek visszaállítása | Támogatott (egy olyan virtuális gép esetében, amelyről január 2019 után biztonsági másolat készül, és ahol rendelkezésre áll a [rendelkezésre állási zónák](https://azure.microsoft.com/global-infrastructure/availability-zones/) ).<br/><br/>Jelenleg a virtuális gépeken rögzített zónára történő visszaállítást támogatjuk. Ha azonban a zóna áramkimaradás miatt nem érhető el, a visszaállítás sikertelen lesz.
Gen2 virtuális gépek | Támogatott <br> Azure Backup támogatja a [Gen2 virtuális gépek](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/)biztonsági mentését és helyreállítását. Ha ezeket a virtuális gépeket helyreállítási pontról állítják vissza, a rendszer [Gen2 virtuális gépekként](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/)állítja vissza őket.
Azure-beli virtuális gépek biztonsági mentése zárolásokkal | Nem felügyelt virtuális gépek esetén nem támogatott. <br><br> Felügyelt virtuális gépek esetén támogatott.
[Spot virtuális gépek](../virtual-machines/spot-vms.md) | Támogatott. Azure Backup a helyszíni virtuális gépeket normál Azure-beli virtuális gépekként visszaállítja.
[Dedikált Azure-gazdagép](https://docs.microsoft.com/azure/virtual-machines/dedicated-hosts) | Támogatott
Különálló Azure-beli virtuális gépek Windows-tárolóhelyek konfigurálása | Támogatott 

## <a name="vm-storage-support"></a>VM-tároló támogatása

**Összetevő** | **Támogatás**
--- | ---
Azure-beli VM-adatlemezek | Legfeljebb 32 lemezzel rendelkező Azure-beli virtuális gépek biztonsági mentésének támogatása.<br><br> Az Azure-beli virtuális gépek nem felügyelt lemezekkel vagy klasszikus virtuális gépekkel történő biztonsági mentésének támogatása legfeljebb 16 lemezből áll.
Adatlemez mérete | Az egyes lemezek mérete legfeljebb 32 TB lehet, és a virtuális gép összes lemezének maximális 256 TB-os kombinációja.
Tárolási típus | Standard HDD, standard SSD, prémium SSD.
Felügyelt lemezek | Támogatott.
Titkosított lemezek | Támogatott.<br/><br/> A Azure Disk Encryption használatával engedélyezett Azure virtuális gépek biztonsági mentése (az Azure AD-alkalmazással vagy anélkül).<br/><br/> A titkosított virtuális gépek nem állíthatók helyre a fájl/mappa szintjén. A teljes virtuális gépet helyre kell állítani.<br/><br/> Engedélyezheti a titkosítást a Azure Backup által már védett virtuális gépeken.
írásgyorsító engedélyezett lemezek | A 2020. november 23-án csak a Korea középső régiójában (KRC) és a Dél-Afrika északi (SAN) régióiban támogatott a korlátozott számú előfizetés. A támogatott előfizetések esetében a Azure Backup biztonsági mentést készít a virtuális gépekről, amelyek a lemezeket a biztonsági mentés során felgyorsított (WA) lemezek esetében teszik lehetővé.<br><br>A nem támogatott régiók esetében internetkapcsolatra van szükség a virtuális gépen, hogy pillanatképeket készítsen Virtual Machines az WA-mel való használatra.<br><br> **Fontos megjegyzés**: ezekben a nem támogatott régiókban a WA lemezekkel rendelkező virtuális gépeknek internetkapcsolattal kell rendelkezniük a sikeres biztonsági mentéshez (annak ellenére, hogy ezek a lemezek ki vannak zárva a biztonsági mentésből).
Biztonsági mentés & deduplikált virtuális gépek/lemezek visszaállítása | A Azure Backup nem támogatja a lemásolást. További információkért tekintse meg ezt a [cikket](./backup-support-matrix.md#disk-deduplication-support) <br/> <br/>  -Azure Backup nem távolítja el a virtuális gépeket a Recovery Services-tárolóban <br/> <br/>  – Ha a visszaállítás során a rendszer lemásolja a virtuális gépeket, a fájlok nem állíthatók vissza, mert a tároló nem érti a formátumot. A virtuális gép teljes visszaállítását azonban sikeresen elvégezheti.
Lemez hozzáadása a védett virtuális géphez | Támogatott.
Lemez átméretezése védett virtuális gépen | Támogatott.
Megosztott tároló| A virtuális gépek Fürt megosztott kötete (CSV) vagy Scale-Out fájlkiszolgáló használatával történő biztonsági mentése nem támogatott. A CSV-írók valószínűleg sikertelenek lesznek a biztonsági mentés során. A Restore utasításban előfordulhat, hogy a CSV-köteteket tartalmazó lemezek nem jönnek létre.
[Megosztott lemezek](../virtual-machines/disks-shared-enable.md) | Nem támogatott.
ultra SSD lemezek | Nem támogatott. További részletekért tekintse meg ezeket a [korlátozásokat](selective-disk-backup-restore.md#limitations).
[Ideiglenes lemezek](https://docs.microsoft.com/azure/virtual-machines/managed-disks-overview#temporary-disk) | Azure Backup nem készít biztonsági másolatot az ideiglenes lemezekről.

## <a name="vm-network-support"></a>VM-hálózat támogatása

**Összetevő** | **Támogatás**
--- | ---
Hálózati adapterek (NIC-EK) száma | Egy adott Azure-beli virtuálisgép-méret számára támogatott hálózati adapterek maximális száma.<br/><br/> A hálózati adapterek akkor jönnek létre, amikor a virtuális gép a visszaállítási folyamat során jön létre.<br/><br/> A visszaállított virtuális gépen lévő hálózati adapterek száma tükrözi a virtuális gép hálózati adapterek számát a védelem engedélyezésekor. A hálózati adapterek eltávolítása a védelem engedélyezése után nem befolyásolja a darabszámot.
Külső/belső terheléselosztó |Támogatott. <br/><br/> [További](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) információ a virtuális gépek speciális hálózati beállításokkal történő visszaállításáról.
Több fenntartott IP-cím |Támogatott. <br/><br/> [További](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) információ a virtuális gépek speciális hálózati beállításokkal történő visszaállításáról.
Több hálózati adapterrel rendelkező virtuális gépek| Támogatott. <br/><br/> [További](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) információ a virtuális gépek speciális hálózati beállításokkal történő visszaállításáról.
Nyilvános IP-címmel rendelkező virtuális gépek| Támogatott.<br/><br/> Társítson egy meglévő nyilvános IP-címet a hálózati adapterhez, vagy hozzon létre egy címet, és társítsa a hálózati adapterhez a visszaállítás után.
Hálózati biztonsági csoport (NSG) a NIC/subnet-on. |Támogatott.
Statikus IP-cím | Nem támogatott.<br/><br/> Egy visszaállítási pontból létrehozott új virtuális gép egy dinamikus IP-címet kap.<br/><br/> A klasszikus virtuális gépek esetében nem lehet biztonsági másolatot készíteni egy fenntartott IP-címmel rendelkező virtuális gépről, és nincs definiálva végpont.
Dinamikus IP-cím |Támogatott.<br/><br/> Ha a forrás virtuális gép hálózati adaptere dinamikus IP-címzést használ, alapértelmezés szerint a visszaállított virtuális gép hálózati adaptere is ezt fogja használni.
Azure Traffic Manager| Támogatott.<br/><br/>Ha a biztonsági másolatban szereplő virtuális gép Traffic Manager található, manuálisan adja hozzá a visszaállított virtuális gépet ugyanahhoz a Traffic Manager-példányhoz.
Azure DNS |Támogatott.
Egyéni DNS |Támogatott.
Kimenő kapcsolat HTTP-proxyn keresztül | Támogatott.<br/><br/> A hitelesített proxyk nem támogatottak.
Virtuális hálózati szolgáltatásvégpontok| Támogatott.<br/><br/> A tűzfal és a virtuális hálózati Storage-fiók beállításainak minden hálózatról engedélyezniük kell a hozzáférést.

## <a name="vm-security-and-encryption-support"></a>VIRTUÁLIS gépek biztonsági és titkosítási támogatása

Azure Backup támogatja a titkosítást az átvitel közbeni és a REST-adatok esetében:

Az Azure-ba irányuló hálózati forgalom:

- A kiszolgálókról a Recovery Services-tárolóra irányuló biztonsági mentési forgalom titkosítása Advanced Encryption Standard 256 használatával történik.
- Biztonsági mentési adat küldése biztonságos HTTPS-kapcsolaton keresztül történik.
- A biztonsági mentési adatforgalom titkosított formában tárolódik a Recovery Services-tárolóban.
- Csak a titkosítási kulccsal lehet feloldani ezeket az adatfájlokat. A Microsoft nem tudja visszafejteni a biztonsági mentési adatforrásokat.

  > [!WARNING]
  > A tároló beállítása után csak Ön férhet hozzá a titkosítási kulcshoz. A Microsoft soha nem tart fenn másolatot, és nem fér hozzá a kulcshoz. Ha a kulcs rossz helyre került, a Microsoft nem tudja helyreállítani a biztonsági mentési adatokat.

Az adatbiztonságot:

- Az Azure-beli virtuális gépek biztonsági mentésekor be kell állítania a titkosítást a virtuális gépen *belül* .
- A Azure Backup támogatja a Azure Disk Encryption, amely a BitLockert a Windows rendszerű virtuális gépeken és az USA **-beli dm-crypt-** ben használja Linux virtuális gépeken.
- Az Azure Backup a háttérben az [Azure Storage Service Encryption](../storage/common/storage-service-encryption.md) használatával biztosít védelmet az inaktív adatok számára.

**Gép** | **Átvitel közben** | **Nyugalmi állapotban**
--- | --- | ---
Helyszíni Windows rendszerű gépek DPM/MABS nélkül | ![Igen][green] | ![Yes][green]
Azure-beli virtuális gépek | ![Igen][green] | ![Yes][green]
Helyszíni/Azure-beli virtuális gépek DPM-mel | ![Igen][green] | ![Yes][green]
Helyszíni/Azure-beli virtuális gépek MABS-mel | ![Igen][green] | ![Yes][green]

## <a name="vm-compression-support"></a>VM-tömörítés támogatása

A Backup a következő táblázatban összefoglalt biztonsági mentési forgalom tömörítését támogatja. Vegye figyelembe a következőket:

- Az Azure-beli virtuális gépek esetében a virtuálisgép-bővítmény közvetlenül az Azure Storage-fiókból olvassa be az adatokat a Storage hálózaton keresztül. Nem szükséges tömöríteni a forgalmat.
- Ha DPM vagy MABS használ, a sávszélességet az adatok tömörítésével mentheti, mielőtt biztonsági mentést készít a DPM/MABS-re.

**Gép** | **Tömörítés a MABS/DPM (TCP)** | **Tömörítés a tárolóba (HTTPS)**
--- | --- | ---
Helyszíni Windows rendszerű gépek DPM/MABS nélkül | NA | ![Igen][green]
Azure-beli virtuális gépek | NA | NA
Helyszíni/Azure-beli virtuális gépek DPM-mel | ![Igen][green] | ![Yes][green]
Helyszíni/Azure-beli virtuális gépek MABS-mel | ![Igen][green] | ![Igen][green]

## <a name="next-steps"></a>Következő lépések

- [Azure-beli virtuális gépek biztonsági mentése](backup-azure-arm-vms-prepare.md).
- [Windows rendszerű gépek biztonsági mentése](tutorial-backup-windows-server-to-azure.md)biztonsági mentési kiszolgáló nélkül.
- [Állítsa be a MABS](backup-azure-microsoft-azure-backup.md) az Azure-ba történő biztonsági mentéshez, majd készítsen biztonsági másolatot a munkaterhelésekről a MABS.
- [Állítsa be a DPM](backup-azure-dpm-introduction.md) az Azure-ba történő biztonsági mentéshez, majd készítsen biztonsági másolatot a munkaterhelésekről a DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
