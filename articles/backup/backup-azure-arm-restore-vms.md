---
title: Virtuális gépek visszaállítása a Azure Portal használatával
description: Egy Azure-beli virtuális gép visszaállítása helyreállítási pontról a Azure Portal használatával, beleértve a régiók közötti visszaállítás funkciót is.
ms.reviewer: geg
ms.topic: conceptual
ms.date: 08/02/2020
ms.openlocfilehash: 83681d2bb3622857fb9141a3cec79d92d278a814
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568749"
---
# <a name="how-to-restore-azure-vm-data-in-azure-portal"></a>Azure-beli virtuális gépekre vonatkozó Azure Portal visszaállítása

Ez a cikk azt ismerteti, hogyan állíthatja vissza az Azure-beli virtuális gépek adatait a [Azure Backup](backup-overview.md) Recovery Services-tárolókban tárolt helyreállítási pontokból.

## <a name="restore-options"></a>Visszaállítási lehetőségek

A Azure Backup számos módszert kínál a virtuális gépek visszaállítására.

**Visszaállítási lehetőség** | **Részletek**
--- | ---
**Új virtuális gép létrehozása** | Gyorsan létrehoz és elindít egy alapszintű virtuális gépet egy visszaállítási pontból.<br/><br/> Megadhatja a virtuális gép nevét, kiválaszthatja azt az erőforráscsoportot és virtuális hálózatot (VNet), amelyben a rendszer elhelyezi, majd megadhat egy Storage-fiókot a visszaállított virtuális géphez. Az új virtuális gépet a forrás virtuális gépével azonos régióban kell létrehozni.<br><br>Ha egy virtuális gép visszaállítása sikertelen, mert egy Azure-beli virtuális gép SKU-je nem volt elérhető az Azure megadott régiójában, vagy bármely más probléma miatt, Azure Backup továbbra is visszaállítja a megadott erőforráscsoport lemezeit.
**Lemez visszaállítása** | Visszaállítja egy virtuális gép lemezét, amely felhasználható egy új virtuális gép létrehozására.<br/><br/> Az Azure Backup biztosít egy sablont, amely segít a virtuális gépek testreszabásában és létrehozásában. <br/><br> A visszaállítási feladattípus létrehoz egy sablont, amelyet letöltheti, és használhatja az egyéni virtuálisgép-beállítások megadására és a virtuális gép létrehozásához.<br/><br/> A lemezek a megadott erőforráscsoportba vannak másolva.<br/><br/> Azt is megteheti, hogy csatlakoztatja a lemezt egy meglévő virtuális géphez, vagy létrehozhat egy új virtuális gépet a PowerShell használatával.<br/><br/> Ez a lehetőség olyankor hasznos, ha testre szeretné szabni a virtuális gépet, olyan konfigurációs beállításokat szeretne hozzáadni, amelyek a biztonsági másolat készítésekor még nem voltak meg, vagy olyan beállításokat szeretne megadni, amelyek a sablon vagy a PowerShell használatával konfigurálhatók.
**Meglévő cseréje** | Visszaállíthat egy lemezt, és lecserélheti a meglévő virtuális gép lemezét.<br/><br/> Az aktuális virtuális gépnek léteznie kell. Ha az már törölve lett, ez a lehetőség nem használható.<br/><br/> Azure Backup a lemez cseréje előtt pillanatképet készít a meglévő virtuális gépről, és a megadott átmeneti helyen tárolja azt. A virtuális képhez csatlakoztatott meglévő lemezek a kiválasztott visszaállítási ponttal lesznek felülírva.<br/><br/> A rendszer átmásolja a pillanatképet a tárolóba, és megőrzi a megőrzési szabályzatnak megfelelően. <br/><br/> A lemez cseréje művelet után az eredeti lemez megmarad az erőforráscsoporthoz. Dönthet úgy is, hogy manuálisan törli az eredeti lemezeket, ha azok nem szükségesek. <br/><br/>A meglévő cseréje nem titkosított felügyelt virtuális gépek esetén támogatott, beleértve az [Egyéni rendszerképekkel létrehozott](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/)virtuális gépeket is. A klasszikus virtuális gépek és a nem felügyelt virtuális gépek esetében nem támogatott.<br/><br/> Ha a visszaállítási pont több vagy kevesebb lemezzel rendelkezik, mint az aktuális virtuális gép, akkor a visszaállítási pontban lévő lemezek száma csak a virtuális gép konfigurációját fogja tükrözni.<br><br> A meglévőt lecserélve a társított erőforrásokkal rendelkező virtuális gépek, például a [felhasználó által hozzárendelt felügyelt identitás](../active-directory/managed-identities-azure-resources/overview.md) vagy [Key Vault](../key-vault/general/overview.md)is támogatott.
**Régiók közötti (másodlagos régió)** | A régiók közötti visszaállítással visszaállíthatja az Azure-beli virtuális gépeket a másodlagos régióban, amely egy [Azure párosított régió](../best-practices-availability-paired-regions.md#what-are-paired-regions).<br><br> A kijelölt helyreállítási ponthoz tartozó összes Azure-beli virtuális gépet visszaállíthatja, ha a biztonsági mentést a másodlagos régióban hajtja végre.<br><br> A biztonsági mentés során a pillanatképek nem replikálódnak a másodlagos régióba. A rendszer csak a tárban tárolt adattárakat replikálja. Így a másodlagos régió visszaállítása csak a [Vault-rétegek](about-azure-vm-restore.md#concepts) visszaállítását teszi elérhetővé. A másodlagos régió visszaállítási ideje majdnem ugyanaz lesz, mint az elsődleges régióhoz tartozó tárolói rétegek visszaállítási ideje.  <br><br> Ez a funkció az alábbi lehetőségekhez érhető el:<br> <li> [Virtuális gép létrehozása](#create-a-vm) <br> <li> [Lemezek visszaállítása](#restore-disks) <br><br> Jelenleg nem támogatott a [meglévő lemezek cseréje](#replace-existing-disks) lehetőség.<br><br> Engedélyek<br> A másodlagos régió visszaállítási művelete a biztonsági mentési rendszergazdák és az alkalmazás-rendszergazdák által végezhető el.

> [!NOTE]
> Az Azure-beli virtuális gépeken meghatározott fájlokat és mappákat is helyreállíthatja. [További információk](backup-azure-restore-files-from-vm.md).

## <a name="storage-accounts"></a>Tárfiókok

A Storage-fiókok részletei:

- **Virtuális gép létrehozása**: új virtuális gép létrehozásakor a virtuális gép a megadott Storage-fiókba kerül.
- **Lemez visszaállítása**: a lemez visszaállításakor a rendszer átmásolja a lemezt a megadott Storage-fiókba. A visszaállítási feladattípus létrehoz egy sablont, amelyet letöltheti és használhatja az egyéni virtuálisgép-beállítások megadásához. Ez a sablon a megadott Storage-fiókba kerül.
- **Lemez cseréje**: Ha egy meglévő virtuális gépen cserél le egy lemezt, Azure Backup a lemez cseréje előtt pillanatképet készít a meglévő virtuális gépről. A pillanatképet az adatátviteli folyamat során is átmásolja a Recovery Services-tárolóba. A pillanatkép-fázis befejeződése után azonban a lemezek cseréje művelet elindul. A lemez cseréje művelet után a forrás Azure-beli virtuális gép lemezei a művelethez tartozó megadott erőforráscsoporthoz maradnak, és a virtuális merevlemezek a megadott Storage-fiókban tárolódnak. Dönthet úgy, hogy törli vagy megőrzi ezeket a VHD-ket és lemezeket.
- **Storage-fiók helye**: a Storage-fióknak ugyanabban a régióban kell lennie, mint a tárolónak. Csak ezek a fiókok jelennek meg. Ha a helyen nincsenek Storage-fiókok, létre kell hoznia egyet.
- **Tárolás típusa**: a blob Storage nem támogatott.
- **Tárolási redundancia**: a zóna redundáns tárolási (ZRS) nem támogatott. A fiók replikációs és redundancia-információi a fiók neve után zárójelben jelennek meg.
- **Prémium szintű Storage**:
  - A nem prémium szintű virtuális gépek visszaállításakor a Premium Storage-fiókok nem támogatottak.
  - A felügyelt virtuális gépek visszaállításakor a hálózati szabályokkal konfigurált Premium Storage-fiókok nem támogatottak.

## <a name="before-you-start"></a>Előkészületek

Egy virtuális gép visszaállításához (hozzon létre egy új virtuális gépet) ellenőrizze, hogy rendelkezik-e a megfelelő Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC) [engedélyekkel](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) a virtuális gép visszaállítása művelethez.

Ha nem rendelkezik megfelelő engedélyekkel, [visszaállíthat egy lemezt](#restore-disks), majd a lemez visszaállítása után a visszaállítási művelet részeként létrehozott [sablont használhatja](#use-templates-to-customize-a-restored-vm) új virtuális gép létrehozásához.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="select-a-restore-point"></a>Válasszon egy visszaállítási pontot

1. A visszaállítani kívánt virtuális géphez társított **tárolóban** válassza az Azure-beli  >  **virtuális gép** biztonsági mentése elemet.
1. Válasszon ki egy virtuális gépet. Alapértelmezés szerint a virtuális gép irányítópultján az utolsó 30 nap helyreállítási pontjai jelennek meg. 30 napnál régebbi helyreállítási pontokat jeleníthet meg, vagy szűréssel megkeresheti a dátumokon, időtartományokon és a pillanatkép-konzisztencia különböző típusain alapuló helyreállítási pontokat.
1. A virtuális gép visszaállításához válassza a **virtuális gép visszaállítása** lehetőséget.

    ![Visszaállítási pont](./media/backup-azure-arm-restore-vms/restore-point.png)

1. Válassza ki a helyreállításhoz használni kívánt visszaállítási pontot.

## <a name="choose-a-vm-restore-configuration"></a>Virtuális gép visszaállítási konfigurációjának kiválasztása

1. A **virtuális gép visszaállítása** lapon válasszon egy visszaállítási lehetőséget:
    - **Új létrehozása**: akkor használja ezt a beállítást, ha új virtuális gépet szeretne létrehozni. Létrehozhat egy egyszerű beállításokkal rendelkező virtuális gépet, vagy visszaállíthat egy lemezt, és létrehozhat egy testreszabott virtuális gépet.
    - **Meglévő cseréje**: akkor használja ezt a beállítást, ha egy meglévő virtuális gépen szeretné lecserélni a lemezeket.

        ![Virtuális gép konfigurációjának visszaállítása varázsló](./media/backup-azure-arm-restore-vms/restore-configuration.png)

1. Adja meg a kijelölt visszaállítási beállítás beállításait.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

A [visszaállítási lehetőségek](#restore-options)egyike gyorsan létrehozhat egy virtuális gépet egy visszaállítási pont alapbeállításaival.

1. A **virtuális gép visszaállítása**  >  **új**  >  **visszaállítási típus** létrehozásához válassza **a virtuális gép létrehozása** lehetőséget.
1. A **virtuális gép neve** mezőben adjon meg egy olyan virtuális gépet, amely nem létezik az előfizetésben.
1. Az **erőforráscsoport** területen válasszon ki egy meglévő erőforráscsoportot az új virtuális géphez, vagy hozzon létre egy újat globálisan egyedi névvel. Ha már létező nevet rendel, az Azure a virtuális géppel megegyező nevű csoportot rendeli hozzá.
1. A **Virtual Network (virtuális hálózat**) területen válassza ki azt a VNet, amelyben a virtuális gép el lesz helyezve. Megjelenik az előfizetéshez társított összes virtuális hálózatok. Válassza ki az alhálózatot. Alapértelmezés szerint az első alhálózat van kiválasztva.
1. Az **előkészítési hely** mezőben válassza ki a virtuális gép Storage-fiókját. [További információk](#storage-accounts).

    ![Konfiguráció visszaállítása varázsló – a visszaállítási beállítások kiválasztása](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

1. Válassza a **visszaállítás** lehetőséget a visszaállítási művelet elindításához.

## <a name="restore-disks"></a>Lemezek visszaállítása

Az egyik [visszaállítási lehetőségként](#restore-options)létrehozhat egy lemezt egy visszaállítási pontból. Ezután a lemezzel a következő műveletek egyikét végezheti el:

- A visszaállítási művelet során létrehozott sablon használatával testreszabhatja a beállításokat, és aktiválhatja a virtuális gépek üzembe helyezését. Szerkessze az alapértelmezett sablon beállításait, és küldje el a sablont a virtuális gép telepítéséhez.
- [Visszaállított lemezek csatolása](../virtual-machines/windows/attach-managed-disk-portal.md) egy meglévő virtuális géphez.
- [Hozzon létre egy új virtuális gépet](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks) a helyreállított lemezekről a PowerShell használatával.

1. A **visszaállítási konfiguráció**  >  **új**  >  **visszaállítási típus** létrehozása területén válassza a **lemezek visszaállítása** lehetőséget.
1. Az **erőforráscsoport** területen válasszon ki egy meglévő erőforráscsoportot a visszaállított lemezekhez, vagy hozzon létre egy újat globálisan egyedi névvel.
1. Az **átmeneti hely** mezőben válassza ki azt a Storage-fiókot, amelyre a VHD-ket másolni szeretné. [További információk](#storage-accounts).

    ![Erőforráscsoport és előkészítési hely kiválasztása](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

1. Válassza a **visszaállítás** lehetőséget a visszaállítási művelet elindításához.

Ha a virtuális gép felügyelt lemezeket használ, és kiválasztja a **virtuális gép létrehozása** lehetőséget, Azure Backup nem a megadott Storage-fiókot használja. **Visszaállítási lemezek** és **azonnali visszaállítás** esetén a Storage-fiók csak a sablon tárolására szolgál. A felügyelt lemezek a megadott erőforráscsoporthoz jönnek létre.
Ha a virtuális gép nem felügyelt lemezeket használ, a rendszer blobként állítja vissza őket a Storage-fiókba.

### <a name="use-templates-to-customize-a-restored-vm"></a>Sablonok használata visszaállított virtuális gép testreszabásához

A lemez visszaállítása után használja a visszaállítási művelet részeként létrehozott sablont az új virtuális gép testreszabásához és létrehozásához:

1. A **biztonsági mentési feladatok** területen válassza ki a megfelelő visszaállítási feladatot.

1. A **visszaállítás** területen válassza a sablon **üzembe helyezése** lehetőséget a sablon központi telepítésének elindításához.

    ![Visszaállítási feladatok részletezése](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

1. A sablonban megadott virtuálisgép-beállítás testreszabásához válassza a **Sablon szerkesztése** lehetőséget. Ha további testreszabásokat szeretne hozzáadni, válassza a **Paraméterek szerkesztése** lehetőséget.
    - [További](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) információ az erőforrások egyéni sablonból való üzembe helyezéséről.
    - [További](../azure-resource-manager/templates/template-syntax.md) információ a sablonok létrehozásáról.

   ![Sablon üzembe helyezése](./media/backup-azure-arm-restore-vms/edit-template1.png)

1. Adja meg a virtuális gép egyéni értékeit, fogadja el a **használati feltételeket** , majd válassza a **vásárlás** lehetőséget.

   ![Sablon központi telepítésének elküldése](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="replace-existing-disks"></a>Meglévő lemezek cseréje

A [visszaállítási lehetőségek](#restore-options)egyike lecserélheti a meglévő virtuálisgép-lemezeket a kijelölt visszaállítási pontra. [Tekintse át](#restore-options) az összes visszaállítási beállítást.

1. A **visszaállítási konfiguráció** területen válassza a **meglévő cseréje** elemet.
1. A **visszaállítás típusa** területen válassza a **lemez/mp cseréje** lehetőséget. Ez az a visszaállítási pont, amelyet a rendszer a meglévő VM-lemezek cseréjére használ.
1. Az **előkészítési hely** területen adja meg, hogy az aktuális felügyelt lemezek pillanatképei a visszaállítási folyamat során legyenek mentve. [További információk](#storage-accounts).

   ![Konfiguráció visszaállítása varázsló meglévő cseréje](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

## <a name="cross-region-restore"></a>Régiók közötti visszaállítás

A [visszaállítási lehetőségek](#restore-options)egyike, a régiók közötti visszaállítás (CRR) lehetővé teszi az Azure-beli virtuális gépek visszaállítását egy másodlagos régióban, amely egy Azure-beli párosított régió.

A szolgáltatás használatának megkezdéséhez olvassa el az [első lépések szakaszt](./backup-create-rs-vault.md#set-cross-region-restore).

Ha szeretné megtudni, hogy a CRR engedélyezve van-e, kövesse a [tartományok közötti visszaállítás konfigurálása](backup-create-rs-vault.md#configure-cross-region-restore)című témakör útmutatását.

### <a name="view-backup-items-in-secondary-region"></a>Biztonsági másolati elemek megtekintése a másodlagos régióban

Ha a CRR engedélyezve van, megtekintheti a másodlagos régió biztonsági másolati elemeit.

1. A portálon nyissa meg **Recovery Services**  >  **tároló biztonsági másolati elemeit**.
1. Válassza a **másodlagos régió** elemet a másodlagos régió elemeinek megtekintéséhez.

>[!NOTE]
>A listában csak a CRR funkciót támogató biztonsági mentési felügyeleti típusok jelennek meg. Jelenleg csak a másodlagos régióba tartozó adatelemek másodlagos régióba való visszaállításának támogatása engedélyezett.

![Virtuális gépek a másodlagos régióban](./media/backup-azure-arm-restore-vms/secbackedupitem.png)

![Másodlagos régió kiválasztása](./media/backup-azure-arm-restore-vms/backupitems-sec.png)

### <a name="restore-in-secondary-region"></a>Visszaállítás másodlagos régióban

A másodlagos régió visszaállítási felhasználói felülete hasonló lesz az elsődleges régió visszaállítási felhasználói felületéhez. Amikor konfigurálja a részleteket a visszaállítási konfiguráció ablaktáblán a visszaállítás konfigurálásához, a rendszer kérni fogja, hogy csak a másodlagos régió paramétereit adja meg.

Jelenleg a másodlagos régió [RPO](azure-backup-glossary.md#rpo-recovery-point-objective) akár 12 órát is igénybe vehet az elsődleges régióból, bár az [olvasási hozzáférésű geo-REDUNDÁNS tárolás (ra-GRS)](../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) replikációja 15 percet vesz igénybe.

![A visszaállítani kívánt virtuális gép kiválasztása](./media/backup-azure-arm-restore-vms/sec-restore.png)

![Visszaállítási pont kiválasztása](./media/backup-azure-arm-restore-vms/sec-rp.png)

![Konfiguráció visszaállítása](./media/backup-azure-arm-restore-vms/rest-config.png)

![Trigger visszaállítása folyamatban lévő értesítésben](./media/backup-azure-arm-restore-vms/restorenotifications.png)

- Virtuális gép visszaállításához és létrehozásához tekintse meg a [virtuális gép létrehozása](#create-a-vm)című témakört.
- Lemezként való visszaállításhoz tekintse meg a [lemezek visszaállítása](#restore-disks)című témakört.

>[!NOTE]
>
>- A visszaállítás elindítása és az adatátviteli fázisban a visszaállítási feladatot nem lehet megszakítani.
>- A régiók közötti visszaállítási funkció visszaállítja a CMK (ügyfél által felügyelt kulcsok) az Azure-beli virtuális gépeket, amelyek nem biztonsági mentést végeznek Recovery Services-tárolón, mivel nem CMK engedélyezett virtuális gépek a másodlagos régióban.
>- A másodlagos régióban való visszaállításhoz szükséges Azure-szerepkörök ugyanazok, mint az elsődleges régióban.

Az [Azure Zone rögzített virtuális gépek](../virtual-machines/windows/create-portal-availability-zone.md) az azonos régió bármely [rendelkezésre állási zónájában](../availability-zones/az-overview.md) visszaállíthatók.

A visszaállítási folyamat során megjelenik a **rendelkezésre állási zóna lehetőség.** Először az alapértelmezett zónát fogja látni. Egy másik zóna kiválasztásához válassza ki a kívánt zóna számát. Ha a rögzített zóna nem érhető el, nem fogja tudni visszaállítani az adott adatkészletet egy másik zónába, mert a biztonsági másolatban szereplő zonally nem replikálódnak. A rendelkezésre állási zónák visszaállítása csak a tároló szintjein lévő helyreállítási pontokból lehetséges.

![Rendelkezésre állási zóna kiválasztása](./media/backup-azure-arm-restore-vms/cross-zonal-restore.png)

### <a name="monitoring-secondary-region-restore-jobs"></a>Másodlagos régió visszaállítási feladatainak figyelése

1. A portálon nyissa meg **Recovery Services**-  >  **tároló biztonsági mentési feladatait**
1. Válassza a **másodlagos régió** elemet a másodlagos régió elemeinek megtekintéséhez.

    ![Biztonsági mentési feladatok szűrve](./media/backup-azure-arm-restore-vms/secbackupjobs.png)

## <a name="restoring-unmanaged-vms-and-disks-as-managed"></a>Nem felügyelt virtuális gépek és lemezek visszaállítása felügyelt

A visszaállítás során lehetősége van arra, hogy a nem [felügyelt lemezeket](../storage/common/storage-disaster-recovery-guidance.md#azure-unmanaged-disks) [felügyelt lemezként](../virtual-machines/managed-disks-overview.md) állítsa vissza. Alapértelmezés szerint a nem felügyelt virtuális gépek/lemezek nem felügyelt virtuális gépek/lemezekként lesznek visszaállítva. Ha azonban úgy dönt, hogy felügyelt virtuális gépeket/lemezeket állít vissza, most már lehetséges. Ezek a visszaállítások nem indulnak el a pillanatkép fázisból, hanem csak a tár fázisában. Ez a funkció nem érhető el a nem felügyelt titkosított virtuális gépekhez.

![Visszaállítás felügyelt lemezként](./media/backup-azure-arm-restore-vms/restore-as-managed-disks.png)

## <a name="restore-vms-with-special-configurations"></a>Virtuális gépek visszaállítása speciális konfigurációkkal

Számos gyakori forgatókönyv esetén előfordulhat, hogy vissza kell állítania a virtuális gépeket.

**Forgatókönyv** | **Útmutató**
--- | ---
**Virtuális gépek visszaállítása Hybrid use Benefit használatával** | Ha egy Windows rendszerű virtuális gép a [hibrid használati juttatás (hub) licencelését](../virtual-machines/windows/hybrid-use-benefit-licensing.md)használja, állítsa vissza a lemezeket, és hozzon létre egy új virtuális gépet a megadott sablonnal (a **licenc típusa** **Windows_Server**) vagy a PowerShell.  Ezt a beállítást a virtuális gép létrehozása után is lehet alkalmazni.
**Virtuális gépek visszaállítása Azure-adatközponti katasztrófa esetén** | Ha a tároló a GRS-t használja, és a virtuális gép elsődleges adatközpontja leáll, a Azure Backup támogatja a biztonsági másolatban lévő virtuális gépeknek a párosított adatközpontba történő visszaállítását. Válasszon egy Storage-fiókot a párosított adatközpontban, és állítsa vissza a szokásos módon. A Azure Backup a párosított régió számítási szolgáltatását használja a visszaállított virtuális gép létrehozásához. [További](/azure/architecture/resiliency/recovery-loss-azure-region) információ az adatközpont rugalmasságáról.<br><br> Ha a tároló GRS használ, kiválaszthatja az új funkciót, a [régiók közötti visszaállítást](#cross-region-restore). Ez lehetővé teszi, hogy a második régióba való visszaállítást teljes vagy részleges kimaradási forgatókönyvekben, vagy még akkor is, ha egyáltalán nincs leállás.
**Operációs rendszer nélküli helyreállítás** | Az Azure-beli virtuális gépek és a helyszíni hypervisorok közötti legnagyobb különbség az, hogy nincs elérhető virtuálisgép-konzol az Azure-ban. Bizonyos helyzetekben szükség van egy konzolra, például egy operációs rendszer nélküli helyreállítás (BMR) típusú biztonsági mentés használatával történő helyreállításra. Azonban a virtuális gépek visszaállítása a tárolóból teljes mértékben helyettesíti a BMR.
**Virtuális gépek visszaállítása speciális hálózati konfigurációkkal** | A speciális hálózati konfigurációk belső vagy külső terheléselosztást használó virtuális gépeket tartalmaznak, több hálózati adapter vagy több fenntartott IP-cím használatával. Ezeket a virtuális gépeket a [lemez visszaállítása lehetőség](#restore-disks)használatával állíthatja vissza. Ez a beállítás lehetővé teszi a virtuális merevlemezek másolatát a megadott Storage-fiókba, és ezután létrehozhat egy [belső](../load-balancer/quickstart-load-balancer-standard-internal-powershell.md) vagy [külső](../load-balancer/quickstart-load-balancer-standard-public-powershell.md) terheléselosztó, [több hálózati adapter](../virtual-machines/windows/multiple-nics.md)vagy [több fenntartott IP-cím](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md)használatával létrehozott virtuális gépet a konfigurációnak megfelelően.
**Hálózati biztonsági csoport (NSG) hálózati ADAPTERen/alhálózaton** | Az Azure virtuális gép biztonsági mentése támogatja a NSG-adatok biztonsági mentését és visszaállítását a vnet, az alhálózat és a NIC szintjén.
**Zónában rögzített virtuális gépek** | Ha olyan Azure-beli virtuális gépet készít biztonsági mentésre, amely egy zónához van rögzítve (Azure Backup), akkor visszaállíthatja azt ugyanabban a zónában, ahol rögzítve van. [További információ](../availability-zones/az-overview.md)
**Virtuális gép visszaállítása bármely rendelkezésre állási csoportból** | Ha egy virtuális gépet a portálról állít helyre, nincs lehetőség a rendelkezésre állási csoport kiválasztására. A helyreállított virtuális gépek nem rendelkeznek rendelkezésre állási csoporttal. Ha a lemez visszaállítása lehetőséget használja, [megadhat egy rendelkezésre állási készletet](../virtual-machines/windows/tutorial-availability-sets.md) , ha a megadott sablonnal vagy PowerShell-lel hozza létre a virtuális gépet a lemezről.
**Speciális virtuális gépek, például SQL virtuális gépek visszaállítása** | Ha az Azure virtuális gép biztonsági mentésével készít biztonsági másolatot egy SQL virtuális gépről, majd a virtuális gép visszaállítása lehetőséget használja, vagy a lemezek visszaállítása után létrehoz egy virtuális gépet, akkor az újonnan létrehozott virtuális gépnek regisztrálnia kell az SQL-szolgáltatóban az [itt](../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md?tabs=azure-cli%2cbash)leírtak szerint. Ezzel átalakítja a visszaállított virtuális gépet egy SQL-virtuális gépre.

### <a name="restore-domain-controller-vms"></a>Tartományvezérlő virtuális gépek visszaállítása

**Forgatókönyv** | **Útmutató**
--- | ---
**Egyetlen tartományvezérlő virtuális gép visszaállítása egyetlen tartományból** | Állítsa vissza a virtuális gépet, mint bármely más virtuális gépet. Vegye figyelembe:<br/><br/> Egy Active Directory perspektívából az Azure-beli virtuális gép olyan, mint bármely más virtuális gép.<br/><br/> A Címtárszolgáltatások helyreállító módja (címtárszolgáltatás-javító mód) is elérhető, így az összes Active Directory-helyreállítási forgatókönyv életképes. [További](#post-restore-steps) információ a virtualizált tartományvezérlők biztonsági mentési és visszaállítási szempontjairól.
**Több tartományvezérlőt tartalmazó virtuális gép visszaállítása egyetlen tartományból** | Ha ugyanabban a tartományban más tartományvezérlők is elérhetők a hálózaton keresztül, akkor a tartományvezérlőt bármely virtuális géphez hasonlóan vissza lehet állítani. Ha a tartomány utolsó fennmaradó tartományvezérlője, vagy egy elszigetelt hálózatban lévő helyreállítást hajt végre, használjon [erdő-helyreállítást](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Egyetlen tartományvezérlő virtuális gép visszaállítása több tartományi konfigurációban** |  Lemezek visszaállítása és virtuális gép létrehozása a [PowerShell használatával](backup-azure-vms-automation.md#restore-the-disks)  
**Több tartomány visszaállítása egy erdőben** | Az [erdő helyreállítását](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)javasoljuk.

További információ: [Active Directory tartományvezérlők biztonsági mentése és visszaállítása](active-directory-backup-restore.md).

## <a name="track-the-restore-operation"></a>A visszaállítási művelet nyomon követése

A visszaállítási művelet elindítását követően a Backup szolgáltatás létrehoz egy feladatot a követéshez. A Azure Backup megjeleníti a feladatokkal kapcsolatos értesítéseket a portálon. Ha nem láthatók, válassza az **értesítések** szimbólumot, majd a **tevékenység naplójában válassza a további események** lehetőséget a visszaállítási folyamat állapotának megtekintéséhez.

![A visszaállítás aktiválva](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 A visszaállítás követése a következőképpen történik:

1. A feladatokhoz tartozó műveletek megtekintéséhez válassza az értesítések hiperhivatkozást. Másik lehetőségként a **tárolóban válassza a biztonsági mentési feladatok** lehetőséget, majd válassza ki a megfelelő virtuális gépet.

    ![A tárolóban lévő virtuális gépek listája](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

1. A visszaállítási folyamat figyeléséhez válassza ki a **folyamatban** állapotú visszaállítási feladatot. Ekkor megjelenik a folyamatjelző sáv, amely a visszaállítási folyamattal kapcsolatos információkat jeleníti meg:

    - A **visszaállítás becsült ideje**: először a visszaállítási művelet befejezéséhez szükséges időt adja meg. A művelet előrehaladtával az igénybe vett idő csökkenti, és a visszaállítási művelet befejeződése után nullára ér.
    - **A visszaállítás százalékos értéke**. Megjeleníti a végrehajtott visszaállítási művelet százalékos arányát.
    - **Átvitt bájtok száma**: Ha új virtuális gép létrehozásával állítja vissza, az átvitt bájtok száma az átvitt bájtok teljes számát mutatja.

## <a name="post-restore-steps"></a>Visszaállítás utáni lépések

A virtuális gépek visszaállítása után néhány dolgot érdemes megjegyezni:

- A biztonsági mentési konfigurációban lévő bővítmények telepítve vannak, de nem engedélyezettek. Ha problémát tapasztal, telepítse újra a bővítményeket.
- Ha a biztonsági másolatban szereplő virtuális gép statikus IP-címmel rendelkezik, a visszaállított virtuális gép dinamikus IP-címmel fog rendelkezni, hogy elkerülje az ütközést. [Statikus IP-címet adhat hozzá a visszaállított virtuális géphez](/powershell/module/az.network/set-aznetworkinterfaceipconfig#description).
- A helyreállított virtuális gépek nem rendelkeznek rendelkezésre állási csoporttal. Ha a lemez visszaállítása lehetőséget használja, [megadhat egy rendelkezésre állási készletet](../virtual-machines/windows/tutorial-availability-sets.md) , ha a megadott sablonnal vagy PowerShell-lel hozza létre a virtuális gépet a lemezről.
- Ha felhő-init-alapú Linux-disztribúciót, például Ubuntut használ, biztonsági okokból a rendszer letiltja a jelszót a visszaállítás után. A [jelszó alaphelyzetbe állításához](/troubleshoot/azure/virtual-machines/reset-password)használja a VISSZAÁLLÍTOTT virtuális gép VMAccess bővítményét. Javasoljuk, hogy SSH-kulcsokat használjon ezeken a disztribúciókban, így a visszaállítás után nem kell alaphelyzetbe állítania a jelszót.
- Ha a visszaállítást követően nem tud hozzáférni egy virtuális géphez, mert a virtuális gép sérült kapcsolattal rendelkezik a tartományvezérlővel, kövesse az alábbi lépéseket a virtuális gép üzembe helyezéséhez:
  - OPERÁCIÓSRENDSZER-lemez csatlakoztatása adatlemezként egy helyreállított virtuális géphez.
  - Manuálisan telepítse a virtuálisgép-ügynököt, ha az Azure-ügynök nem válaszol a [hivatkozás](/troubleshoot/azure/virtual-machines/install-vm-agent-offline)követésével.
  - A soros konzol elérésének engedélyezése a virtuális gépen a virtuális gép parancssori elérésének engedélyezéséhez

  ```cmd
    bcdedit /store <drive letter>:\boot\bcd /enum
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<<BOOT LOADER IDENTIFIER>>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```

  - A virtuális gép újraépítése Azure Portal a helyi rendszergazdai fiók és a jelszó alaphelyzetbe állításához
  - A virtuális gép leválasztása Serial console hozzáférés és a CMD használatával a tartományból

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```

- A virtuális gép leválasztása és újraindítása után a virtuális gép a helyi rendszergazdai hitelesítő adatokkal, majd a virtuális gép sikeresen újracsatlakoztatható a tartományba.

## <a name="backing-up-restored-vms"></a>Visszaállított virtuális gépek biztonsági mentése

- Ha egy virtuális gépet ugyanarra az erőforráscsoporthoz állítja vissza, mint az eredetileg mentett virtuális gép neve, a Backup a virtuális gépen folytatódik a visszaállítás után.
- Ha egy másik erőforráscsoporthoz állítja vissza a virtuális gépet, vagy ha a visszaállított virtuális gép számára más nevet adott meg, be kell állítania a visszaállított virtuális gép biztonsági mentését.

## <a name="next-steps"></a>Következő lépések

- Ha a visszaállítási folyamat során nehézségekbe ütközik, [tekintse át](backup-azure-vms-troubleshoot.md#restore) a gyakori problémákat és hibákat.
- A virtuális gép visszaállítása után megismerheti a [virtuális gépek kezelését](backup-azure-manage-vms.md) .