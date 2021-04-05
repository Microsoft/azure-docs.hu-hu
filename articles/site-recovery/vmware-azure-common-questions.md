---
title: Gyakori kérdések a VMware vész-helyreállítással kapcsolatban Azure Site Recovery
description: Választ kaphat a helyszíni VMware virtuális gépek Azure-ba való vész-helyreállításával kapcsolatos gyakori kérdésekre Azure Site Recovery használatával.
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: a272486eea111ab8c8e489556986f12f382e3f65
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97587792"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>Gyakori kérdések VMware-ből az Azure-ba történő replikációról

Ez a cikk a helyszíni VMware virtuális gépek (VM-EK) Azure-ba történő vész-helyreállításának telepítésekor felmerülő gyakori kérdésekre ad választ.

## <a name="general"></a>Általános kérdések

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>Mire van szükség a VMware virtuális gépek vész-helyreállításához?

[Ismerje meg a](vmware-azure-architecture.md) VMWare virtuális gépek vész-helyreállításában részt vevő összetevőket.

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>Használhatom a VMware virtuális gépek Azure-ba való áttelepítésére Site Recovery?

Igen. A VMware virtuális gépek teljes vész-helyreállításának beállításán kívül a Site Recovery használata a helyszíni VMware virtuális gépek Azure-ba való áttelepítésére is használható Site Recovery. Ebben az esetben a helyszíni VMware virtuális gépeket az Azure Storage-ba replikálja. Ezt követően feladatátvételt végez a helyszínről az Azure-ba. A feladatátvételt követően az alkalmazások és a számítási feladatok elérhetők és futnak az Azure-beli virtuális gépeken. A folyamat olyan, mint a teljes vész-helyreállítás beállítása, kivéve, ha az áttelepítés során nem tud visszavenni az Azure-ból.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Szükség van az Azure-fióknak a virtuális gépek létrehozásához szükséges engedélyekre?

Ha Ön előfizetés-rendszergazda, a szükséges replikációs engedélyekkel rendelkezik. Ha Ön nem rendszergazda, akkor a következő műveletek elvégzéséhez szükséges engedélyekkel kell rendelkeznie:

- Hozzon létre egy Azure-beli virtuális gépet a Site Recovery konfigurálásakor megadott erőforráscsoport és virtuális hálózat között.
- A konfiguráció alapján írjon a kiválasztott Storage-fiókba vagy a felügyelt lemezre.

[További](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) információ a szükséges engedélyekről.

### <a name="what-applications-can-i-replicate"></a>Milyen alkalmazásokat lehet replikálni?

Bármely olyan alkalmazást vagy munkaterhelést replikálhat, amely a [replikálási követelményeknek](vmware-physical-azure-support-matrix.md#replicated-machines)megfelelő VMWare virtuális gépen fut.

- A Site Recovery támogatja az alkalmazások közötti replikálást, így az alkalmazások feladatátvétele és visszahívása intelligens állapotba sikertelen lehet.
- A Site Recovery a Microsoft-alkalmazásokkal, például a SharePoint, az Exchange, a Dynamics, a SQL Server és a Active Directory szolgáltatással integrálható. Emellett szorosan együttműködik az olyan vezető szállítókkal, mint az Oracle, az SAP, az IBM vagy a Red Hat.

[További információk](site-recovery-workload.md) a számítási feladatok védelméről.

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>Használhatok vendég operációs rendszerbeli kiszolgálói licencet az Azure-ban?

Igen, a Microsoft frissítési garanciával rendelkező ügyfelei [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) használatával menthetik a licencelési költségeket az Azure-ba migrált Windows Server-gépekre, illetve az Azure-t a vész-helyreállításra.

## <a name="security"></a>Biztonság

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>Milyen Site Recovery szükséges a VMware-kiszolgálókhoz való hozzáférés?

A Site Recoverynek hozzáféréssel kell rendelkeznie a VMware-kiszolgálókhoz az alábbiak érdekében:

- Állítson be egy VMware virtuális gépet, amely a Site Recovery konfigurációs kiszolgálót futtatja.
- Virtuális gépek automatikus felderítése replikáláshoz.

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>Milyen Site Recovery szükséges a VMware virtuális gépekhez való hozzáférés?

- A replikáláshoz a VMware virtuális gépnek telepítve és futnia kell a Site Recovery mobilitási szolgáltatásnak. Az eszközt manuálisan is üzembe helyezheti, vagy megadhatja, hogy a virtuális gép replikálásának engedélyezésekor Site Recovery a szolgáltatás leküldéses telepítését.
- A replikáció során a virtuális gépek a következőképpen kommunikálnak a Site Recoveryekkel:
    - A virtuális gépek a replikációs felügyelethez a 443-es HTTPS-porton keresztül kommunikálnak a konfigurációs kiszolgálóval.
    - A virtuális gépek replikációs adatküldést küldenek a Process Servernek a 9443-es HTTPS-porton. (Ez a beállítás módosítható.)
    - Ha engedélyezi a több virtuális gépre kiterjedő konzisztenciát, a virtuális gépek a 20004-es porton keresztül kommunikálnak egymással.

### <a name="is-replication-data-sent-to-site-recovery"></a>A replikációs adatküldés a Site Recovery?

Nem, Site Recovery nem metszi a replikált adatokat, és nem rendelkezik információval arról, hogy mi fut a virtuális gépeken. A replikációs adatcsere a VMware hypervisors és az Azure Storage között történik. A Site Recovery nem képes ezekhez az adatokhoz hozzáférni. A Site Recovery szolgáltatás csak a replikáció és a feladatátvétel levezényléséhez szükséges metaadatokat kapja meg.

A Site Recovery ISO 27001:2013 és 27018, HIPAA és DPA minősítéssel rendelkezik. Ez a folyamat SOC2 és FedRAMP ÜSS értékeléseket.

## <a name="pricing"></a>Díjszabás

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>Hogyan kiszámítja a VMware vész-helyreállítás becsült díjait?

A [díjszabási számológép](https://aka.ms/asr_pricing_calculator) használatával megbecsülheti a költségeket site Recovery használata közben.

A költségek részletes becsléséhez futtassa a Deployment Planner eszközt a [VMware](./site-recovery-deployment-planner.md) -hez, és használja a [Cost becslése jelentést](./site-recovery-vmware-deployment-planner-cost-estimation.md).

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>Van különbség a tárterületre való replikálás és a felügyelt lemezekre való közvetlen váltás között?

A felügyelt lemezeket a Storage-fiókoktól némileg eltérően kell fizetni. [További](https://azure.microsoft.com/pricing/details/managed-disks/) információ a felügyelt lemez díjszabásáról.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Van-e különbség a általános célú v2 Storage-fiókra való replikáláskor?

Általában a GPv2 miatti tranzakciók költségeinek növekedését tapasztalja, mivel Azure Site Recovery tranzakciós terhelést eredményez. [További információ](../storage/common/storage-account-upgrade.md#pricing-and-billing) a változás megbecsléséről.

## <a name="mobility-service"></a>Mobilitási szolgáltatás

### <a name="where-can-i-find-the-mobility-service-installers"></a>Hol találhatom meg a mobilitási szolgáltatás telepítőit?

A telepítők a konfigurációs kiszolgáló%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository mappájában találhatók.

## <a name="how-do-i-install-the-mobility-service"></a>Hogyan telepíteni a mobilitási szolgáltatást?

Minden replikálni kívánt virtuális gépen telepítse a szolgáltatást több módszer egyikével:

- [Leküldéses telepítés](vmware-physical-mobility-service-overview.md#push-installation)
- [Manuális telepítés](vmware-physical-mobility-service-overview.md#install-the-mobility-service-using-ui) a felhasználói felületen vagy a PowerShellben
- Üzembe helyezés egy központi telepítési eszköz használatával, például [Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md)

## <a name="managed-disks"></a>Felügyelt lemezek

### <a name="where-does-site-recovery-replicate-data-to"></a>Hová Site Recovery az adatreplikálás?

Site Recovery a helyszíni VMware virtuális gépeket és fizikai kiszolgálókat replikálja a felügyelt lemezekre az Azure-ban.

- A Site Recovery Process Server a replikációs naplókat a célként megadott régióban lévő cache Storage-fiókba írja.
- Ezek a naplók a **asrseeddisk** előtaggal rendelkező Azure által felügyelt lemezeken helyreállítási pontok létrehozására szolgálnak.
- Feladatátvétel esetén a kiválasztott helyreállítási pont egy új célként felügyelt lemez létrehozására szolgál. Ez a felügyelt lemez az Azure-beli virtuális géphez van csatlakoztatva.
- A korábban a Storage-fiókba replikált virtuális gépek (2019 márciusa előtt) nem érintettek.

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>Tudok új gépeket replikálni a Storage-fiókokba?

Nem. A Azure Portal március 2019-én kezdődően csak az Azure Managed Disks szolgáltatásban lehet replikálni.

Az új virtuális gépek egy Storage-fiókba való replikálása csak a PowerShell ([az. recoveryservices szolgáltatónál modul verziója 1.4.5](https://www.powershellgallery.com/packages/Az.RecoveryServices/1.4.5)) vagy a REST API (2018-01-10-es vagy 2016-08-10-os verzió) használatával érhető el. [Megtudhatja, hogyan](./vmware-azure-disaster-recovery-powershell.md) telepítheti a replikációt a PowerShell-parancsok használatával.

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>Milyen előnyökkel jár a felügyelt lemezekre való replikálás?

[Útmutató](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) Site Recovery leegyszerűsíti a felügyelt lemezekkel való vész-helyreállítást.

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>Módosíthatom a felügyelt lemez típusát a gép védelme után?

Igen, egyszerűen [módosíthatja a felügyelt lemez típusát](../virtual-machines/windows/convert-disk-storage.md) a folyamatos replikáláshoz. A típus módosítása előtt győződjön meg arról, hogy a felügyelt lemezen nem jön létre közös hozzáférésű aláírási URL-cím:

1. Nyissa meg a **felügyelt lemez** erőforrást a Azure Portalon, és győződjön meg arról, hogy rendelkezik-e közös hozzáférési aláírási URL-címmel az **Áttekintés** panelen.
1. Ha a szalagcím megtalálható, válassza ki azt a folyamatban lévő exportálás megszakításához.
1. Módosítsa a lemez típusát a következő néhány percen belül. Ha módosítja a felügyelt lemez típusát, várjon, amíg a Azure Site Recovery új helyreállítási pontokat hoz létre.
1. A jövőbeli feladatátvételi és feladatátvételi tesztekhez használja az új helyreállítási pontokat.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>Válthatok a felügyelt lemezekről a nem felügyelt lemezekre történő replikáció?

Nem. A felügyelt és a nem felügyelt közötti váltás nem támogatott.

## <a name="replication"></a>Replikáció

### <a name="what-are-the-replicated-vm-requirements"></a>Mik a replikált virtuális gépekre vonatkozó követelmények?

[További](vmware-physical-azure-support-matrix.md#replicated-machines) információ a VMWare virtuális gépek és a fizikai kiszolgálók támogatási követelményeiről.

### <a name="how-often-can-i-replicate-to-azure"></a>Milyen gyakran lehet replikálni az Azure-ba?

A VMware virtuális gépek Azure-ba történő replikálásakor a replikálás folyamatos.

### <a name="can-i-extend-replication"></a>Ki lehet terjeszteni a replikálást?

A kiterjesztett vagy láncolt replikáció nem támogatott. Kérje ezt a szolgáltatást a [visszajelzési fórumba](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="how-can-i-track-progress-of-initial-replicationsynchronization"></a>Hogyan követhetem nyomon a kezdeti replikáció/szinkronizálás folyamatát?

Ez a funkció mostanában a szolgáltatások Site Recovery. Pontos részletekért frissítse a Site Recovery-infrastruktúrát (konfigurációs kiszolgálók, kibővített folyamat-kiszolgálók) és a mobilitási ügynököt a 9,36-es vagy újabb verzióra. További információ az előrehaladás nyomon követéséről [itt](vmware-azure-enable-replication.md#monitor-initial-replication).

### <a name="can-i-do-an-offline-initial-replication"></a>Végezhetek kapcsolat nélküli kezdeti replikálást?

Az offline replikáció nem támogatott. Kérje ezt a szolgáltatást a [visszajelzési fórumba](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="what-is-asrseeddisk"></a>Mi az a asrseeddisk?

Minden forrásoldali lemez esetében az Azure-ban egy felügyelt lemezre történik az adatreplikálás. Ez a lemez rendelkezik a **asrseeddisk** előtaggal. Tárolja a forrás lemez másolatát és a helyreállítási pontok összes pillanatképét.

### <a name="can-i-exclude-disks-from-replication"></a>Ki lehet zárni a lemezeket a replikációból?

Igen, kihagyhatja a lemezeket.

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>Replikálhatók a dinamikus lemezekkel rendelkező virtuális gépek?

A dinamikus lemezek replikálhatók. Az operációs rendszer lemezének alapszintű lemeznek kell lennie.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Ha több virtuális gépre kiterjedő konzisztencia-replikációs csoportokat használok, Hozzáadhatok egy új virtuális gépet egy meglévő replikációs csoporthoz?

Igen, új virtuális gépeket adhat hozzá egy meglévő replikációs csoporthoz, amikor engedélyezi a replikálást. Azonban

- A replikálás megkezdése után nem adhat hozzá virtuális gépet meglévő replikációs csoporthoz.
- Meglévő virtuális gépekhez nem lehet replikációs csoportot létrehozni.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Módosíthatom a replikált virtuális gépeket lemezek hozzáadásával vagy átméretezésével?

Az Azure-ba történő VMware-replikáláshoz módosíthatja a forrásként használt virtuális gépek méretét. Ha új lemezeket szeretne hozzáadni, hozzá kell adnia a lemezt, és újra engedélyeznie kell a virtuális gép védelmét.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>Áttelepíthetem a helyszíni gépeket egy új vCenter Serverra anélkül, hogy ez hatással lenne a folyamatos replikációra?

Tekintse át a gépek új vCenter való [átirányítására vonatkozó útmutatást](vmware-azure-manage-vcenter.md#migrate-all-vms-to-a-new-server) .

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>Replikálható egy olyan gyorsítótárba vagy célként megadott Storage-fiókba, amelyen be van állítva a virtuális hálózat (Azure tűzfallal)?

Nem, Site Recovery nem támogatja az Azure Storage-ba való replikálást a virtuális hálózatokon.

### <a name="what-is-the-frequency-of-generation-of-crash-consistent-recovery-points"></a>Milyen gyakorisággal történik az összeomlás-konzisztens helyreállítási pontok létrehozása?

A Site Recovery 5 percenként generál összeomlás-konzisztens helyreállítási pontokat.

## <a name="component-upgrade"></a>Összetevő frissítése

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>A mobilitási szolgáltatások ügynökének vagy a konfigurációs kiszolgálónak a saját verziója elavult, és a frissítés nem sikerült. Mit tegyek?

Site Recovery az N-4 támogatási modellt követi. [További](./service-updates-how-to.md#support-statement-for-azure-site-recovery) információ a nagyon régi verziókról való frissítésről.

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>Hol találhatók a Azure Site Recovery kibocsátási megjegyzései és kumulatív frissítései?

[Ismerje meg az új frissítéseket](site-recovery-whats-new.md), és szerezze be a [kumulatív információkat](service-updates-how-to.md).

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Hol találhatok frissítési információt az Azure-ba való vész-helyreállításhoz?

[További információ a frissítésről](./service-updates-how-to.md#vmware-vmphysical-server-disaster-recovery-to-azure).

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>Szükség van-e az egyes verziófrissítésekhez szükséges forrásoldali gépek újraindítására?

Az újraindítás ajánlott, de nem kötelező az egyes verziófrissítésekhez. [További információk](./service-updates-how-to.md#reboot-after-mobility-service-upgrade).

## <a name="configuration-server"></a>Konfigurációs kiszolgáló

### <a name="what-does-the-configuration-server-do"></a>Mit tesz a konfigurációs kiszolgáló?

A konfigurációs kiszolgáló a helyszíni Site Recovery összetevőket futtatja, beleértve a következőket:

- Maga a konfigurációs kiszolgáló. A kiszolgáló koordinálja a helyszíni összetevők és az Azure közötti kommunikációt, és felügyeli az adatreplikációt.
- A folyamat-kiszolgáló, amely replikációs átjáróként működik. Ez a kiszolgáló:
    1. Fogadja a replikaadatokat.
    2. A gyorsítótárazással, tömörítéssel és titkosítással optimalizálja az adatátvitelt.
    3. Az adatokat az Azure Storage-ba küldi.
  A Process Server a mobilitási szolgáltatás leküldéses telepítését is végrehajtja a virtuális gépeken, és elvégzi a helyszíni VMware virtuális gépek automatikus felderítését.
- A fő célkiszolgáló, amely kezeli a replikálási adatait az Azure-beli feladat-visszavétel során.

[További](vmware-azure-architecture.md) információ a konfigurációs kiszolgáló összetevőiről és folyamatairól.

### <a name="where-do-i-set-up-the-configuration-server"></a>Hol állíthatom be a konfigurációs kiszolgálót?

A konfigurációs kiszolgálóhoz egyetlen, magasan elérhető helyszíni VMware virtuális gép szükséges. A fizikai kiszolgáló vész-helyreállításához telepítse a konfigurációs kiszolgálót egy fizikai gépre.

### <a name="what-do-i-need-for-the-configuration-server"></a>Mire van szükségem a konfigurációs kiszolgálóhoz?

Tekintse át az [előfeltételeket](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Manuálisan is beállítható a konfigurációs kiszolgáló a sablon használata helyett?

Azt javasoljuk, hogy a [konfigurációs kiszolgáló virtuális gépet](vmware-azure-deploy-configuration-server.md) a Open VIRTUALIZATION Format (OVF) sablon legújabb verziójával hozza létre. Ha nem tudja használni a sablont (például ha nem rendelkezik hozzáféréssel a VMware-kiszolgálóhoz), [töltse le](physical-azure-set-up-source.md) a telepítőfájlt a portálról, és állítsa be a konfigurációs kiszolgálót.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Replikálhat egy konfigurációs kiszolgálót több régióban is?

Nem. Ha több régióba szeretne replikálni, szüksége lesz egy konfigurációs kiszolgálóra az egyes régiókban.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Tárolhatok egy konfigurációs kiszolgálót az Azure-ban?

Bár lehetséges, a konfigurációs kiszolgálót futtató Azure-beli virtuális gépnek kommunikálnia kell a helyszíni VMware-infrastruktúrával és a virtuális gépekkel. Ez a kommunikáció késést okoz, és hatással van a folyamatban lévő replikációra.

### <a name="how-do-i-update-the-configuration-server"></a>Hogyan frissíteni a konfigurációs kiszolgálót?

[Útmutató](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) a konfigurációs kiszolgáló frissítéséhez.

- A legújabb frissítési információkat az [Azure Updates lapon](https://azure.microsoft.com/updates/?product=site-recovery)találja.
- A legújabb verziót a portálról töltheti le. A konfigurációs kiszolgáló legújabb verzióját közvetlenül a [Microsoft letöltőközpontból](https://aka.ms/asrconfigurationserver)is letöltheti.
- Ha a verziószáma meghaladja az aktuális verziónál régebbi négy verziót, tekintse meg a frissítési útmutatót a [támogatási utasításban](./service-updates-how-to.md#support-statement-for-azure-site-recovery) .

### <a name="should-i-back-up-the-configuration-server"></a>Biztonsági mentést kell készíteni a konfigurációs kiszolgálóról?

Javasoljuk, hogy rendszeresen ütemezett biztonsági mentést készítsen a konfigurációs kiszolgálóról.

- A sikeres feladat-visszavétel érdekében a sikertelenül visszaadott virtuális gépnek léteznie kell a konfigurációs kiszolgáló adatbázisában.
- A konfigurációs kiszolgálónak futnia kell, és csatlakoztatott állapotban kell lennie.
- [További](vmware-azure-manage-configuration-server.md) információ a közös konfigurációs kiszolgáló felügyeleti feladatairól.

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>A konfigurációs kiszolgáló beállításakor a MySQL-t manuálisan lehet letölteni és telepíteni?

Igen. Töltse le a MySQL-t, és helyezze el a C:\Temp\ASRSetup mappában. Ezután telepítse manuálisan. A konfigurációs kiszolgáló virtuális gépe beállítása és a használati feltételek elfogadása után a MySQL a **letöltés és telepítés** során **már telepítettként** jelenik meg.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>El tudom kerülni a MySQL letöltését, de Site Recovery telepíteni?

Igen. Töltse le a MySQL-telepítőt, és helyezze a C:\Temp\ASRSetup mappába. A konfigurációs kiszolgáló virtuális gép beállításakor fogadja el a feltételeket, és válassza a **letöltés és telepítés** lehetőséget. A portál a MySQL telepítéséhez hozzáadott telepítőt fogja használni.

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>Használhatom a konfigurációs kiszolgáló virtuális gépet bármilyen más számára?

Nem. Csak a konfigurációs kiszolgálóhoz használja a virtuális gépet.

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>Megadhatok egy konfigurációs kiszolgálót, és felhasználhatók az előkészítéshez?

Nem. Hozzon létre egy új konfigurációs kiszolgálót a regisztrációs problémák elkerüléséhez.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>Módosíthatom azt a tárolót, amelyben a konfigurációs kiszolgáló regisztrálva van?

Nem. Miután egy tároló társítva van a konfigurációs kiszolgálóhoz, nem módosítható. [További](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) információ a konfigurációs kiszolgálók egy másik tárolóval való regisztrálásáról.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Használhatom ugyanazt a konfigurációs kiszolgálót a VMware virtuális gépek és fizikai kiszolgálók vész-helyreállítására?

Igen, de vegye figyelembe, hogy a fizikai gép csak a VMware virtuális gépeken végezhető el.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Honnan tölthetők le a konfigurációs kiszolgáló jelszava?

[Útmutató](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) a jelszó letöltéséhez.

### <a name="where-can-i-download-vault-registration-keys"></a>Honnan tölthetők le a tároló regisztrációs kulcsai?

A Recovery Services-tárolóban válassza a **konfigurációs kiszolgálók** lehetőséget **site Recovery infrastruktúra**  >  **kezelése** elemnél. Ezután a **kiszolgálók** területen válassza a **regisztrációs kulcs letöltése** lehetőséget a tár hitelesítő adatainak fájljának letöltéséhez.

### <a name="can-a-single-configuration-server-be-used-to-protect-multiple-vcenter-instances"></a>Használható egyetlen konfigurációs kiszolgáló a több vCenter-példány védeleméhez?

Igen, egyetlen konfigurációs kiszolgáló is képes a virtuális gépeket több vCenter is védelemmel ellátni.  Nincs korlátozva, hogy hány vCenter-példányt lehet hozzáadni a konfigurációs kiszolgálóhoz, azonban az egyetlen konfigurációs kiszolgáló által védett virtuális gépek számának korlátozásait is alkalmazza a rendszer.

### <a name="can-a-single-configuration-server-protect-multiple-clusters-within-vcenter"></a>Egyetlen konfigurációs kiszolgáló is biztosít több fürtöt a vCenter-en belül?

Igen, Azure Site Recovery képes a virtuális gépeket különböző fürtökön keresztül védelemmel ellátni.

## <a name="process-server"></a>Folyamatkiszolgáló

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>Miért nem tudom kiválasztani a Process Servert a replikálás engedélyezésekor?

A 9,24-es és újabb verziókban elérhető frissítések mostantól megjelenítik a [folyamat kiszolgálójának állapotát, amikor engedélyezi a replikációt](vmware-azure-enable-replication.md#enable-replication). Ez a funkció segít elkerülni a folyamatok és a kiszolgálók szabályozását, és minimálisra csökkenti a nem kifogástalan állapotú folyamatok kiszolgálóinak használatát.

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>Hogyan frissíteni a folyamat-kiszolgálót a 9,24-es vagy újabb verzióra a pontos állapotra vonatkozó információk érdekében?

A 9,24-es [verziótól](service-updates-how-to.md#links-to-currently-supported-update-rollups)kezdődően további riasztások lettek hozzáadva a Process Server állapotának jelzéséhez. [Frissítse site Recovery-összetevőit a 9,24-es vagy újabb verzióra](service-updates-how-to.md#links-to-currently-supported-update-rollups) , hogy minden riasztást generáljon.

### <a name="how-can-i-ensure-high-availability-of-the-process-server"></a>Hogyan biztosíthatom a Process Server magas rendelkezésre állását?

Több Process Server konfigurálásával a kialakítás rugalmasságot biztosít a védett gépek a nem kifogástalan állapotú folyamat kiszolgálóról a munkafolyamati kiszolgálóra történő áthelyezéséhez. A gép egyik folyamatból a másikba való áthelyezését explicit módon/manuálisan kell kezdeményezni a megadott lépésekkel, itt: [virtuális gépek áthelyezése a Process](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load)Servers között.

## <a name="failover-and-failback"></a>Feladatátvétel és feladat-visszavétel

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>Használhatom a helyszíni folyamat-kiszolgálót a feladat-visszavételhez?

Javasoljuk, hogy az adatátviteli késések elkerülése érdekében az Azure-ban hozzon létre egy folyamat-kiszolgálót a feladat-visszavétel érdekében. Továbbá abban az esetben, ha a forrásként szolgáló virtuális gépek hálózatát a konfigurációs kiszolgálón lévő Azure-beli hálózattal együtt választja, elengedhetetlen az Azure-ban létrehozott folyamat-kiszolgáló használata a feladat-visszavételhez.

### <a name="can-i-keep-the-ip-address-on-failover"></a>Megtarthatom a feladatátvételi IP-címet?

Igen, megtarthatja a feladatátvételi IP-címet. A feladatátvétel előtt meg kell adnia a cél IP-címet a virtuális gép **számítási és hálózati** beállításaiban. Emellett állítsa le a gépeket a feladatátvétel során, hogy elkerülje az IP-címek ütközését a feladat-visszavétel során.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>Módosíthatom a cél virtuális gép méretét vagy a virtuális gép típusát a feladatátvétel előtt?

Igen, a feladatátvétel előtt bármikor módosíthatja a virtuális gép típusát vagy méretét. A portálon használja a replikált virtuális gép **számítási és hálózati** beállításait.

### <a name="how-far-back-can-i-recover"></a>Meddig lehet visszaállítani?

A VMware-ből az Azure-ba a legrégebbi helyreállítási pont 72 óra lehet.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Hogyan Azure-beli virtuális gépeket a feladatátvételt követően?

A feladatátvételt követően az Azure-beli virtuális gépek biztonságos internetkapcsolaton keresztül, helyek közötti VPN-en vagy Azure-ExpressRoute keresztül érhetők el. A csatlakozáshoz több dolgot kell előkészítenie. [További információk](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

### <a name="is-failed-over-data-resilient"></a>A feladatátvételi feladathoz képest rugalmas?

Az Azure-t hibatűrőnek terveztük. Site Recovery a másodlagos Azure-adatközpontba történő feladatátvételre tervezték, az Azure szolgáltatói szerződése (SLA) által megkövetelt módon. Feladatátvétel esetén győződjön meg arról, hogy a metaadatok és a tárolók ugyanabban a földrajzi régióban maradnak, amelyet a tárolóhoz választott.

### <a name="is-failover-automatic"></a>Automatikus a feladatátvétel?

A [feladatátvétel](site-recovery-failover.md) nem automatikus. A feladatátvételt úgy indítja el, hogy egyetlen kijelölést végez a portálon, vagy a [PowerShell](/powershell/module/az.recoveryservices) használatával elindítja a feladatátvételt.

### <a name="can-i-fail-back-to-a-different-location"></a>Visszatérhetek egy másik helyre?

Igen. Ha a feladatátvételt az Azure-ba hajtja végre, visszatérhet egy másik helyre, ha az eredeti nem érhető el. [További információk](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>Miért van szükségem VPN-vagy ExpressRoute-kapcsolatra a feladat-visszavétel érdekében?

Ha az Azure-ból végez feladatátvételt, az Azure-ból származó adatok visszakerülnek a helyszíni virtuális gépre, és a privát hozzáférésre van szükség.


## <a name="automation-and-scripting"></a>Automatizálás és parancsfájlkezelés

### <a name="can-i-set-up-replication-with-scripting"></a>Beállítható a replikáció parancsfájlok futtatásával?

Igen. A REST API, a PowerShell vagy az Azure SDK használatával automatizálhatja Site Recovery munkafolyamatokat. [További információk](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Teljesítmény és kapacitás

### <a name="can-i-throttle-replication-bandwidth"></a>Szabályozható a replikálási sávszélesség?

Igen. [További információk](site-recovery-plan-capacity-vmware.md).

## <a name="next-steps"></a>Következő lépések

- [Tekintse át](vmware-physical-azure-support-matrix.md) a támogatási követelményeket.
- [Beállítás](vmware-azure-tutorial.md) VMware – Azure replikálás.