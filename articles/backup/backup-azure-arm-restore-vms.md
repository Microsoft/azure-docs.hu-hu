---
title: Virtuális gépek visszaállítása a Azure Portal
description: Azure-beli virtuális gép visszaállítása helyreállítási pontról a Azure Portal használatával, beleértve a régiók közötti visszaállítás funkciót.
ms.reviewer: geg
ms.topic: conceptual
ms.date: 04/14/2021
ms.openlocfilehash: 452ca5e1b1c6554a2ae1651068eae06ad9a6b232
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515226"
---
# <a name="how-to-restore-azure-vm-data-in-azure-portal"></a>Azure-beli virtuális gépek adatainak visszaállítása a Azure Portal

Ez a cikk bemutatja, hogyan állíthatók vissza az Azure-beli virtuális gépek adatai a helyreállítási [Azure Backup](backup-overview.md) helyreállítási tárolókban tárolt helyreállítási pontokról.

## <a name="restore-options"></a>Visszaállítási lehetőségek

Azure Backup virtuális gép visszaállításának számos módját biztosítja.

**Visszaállítási lehetőség** | **Részletek**
--- | ---
**Új virtuális gép létrehozása** | Gyorsan létrehoz és elindít egy alapszintű virtuális gépet egy visszaállítási pontból.<br/><br/> Megadhatja a virtuális gép nevét, kiválaszthatja azt az erőforráscsoportot és virtuális hálózatot (VNet), amelybe a virtuális gépet helyezni fogja, és megadhat egy tárfiókot a visszaállított virtuális géphez. Az új virtuális gépet a forrás virtuális gépével azonos régióban kell létrehozni.<br><br>Ha egy virtuális gép visszaállítása meghiúsul, mert egy Azure-beli virtuális gép termékváltozata nem volt elérhető az Azure adott régiójában, vagy bármilyen más probléma miatt a Azure Backup a megadott erőforráscsoportban lévő lemezeket is visszaállítja.
**Lemez visszaállítása** | Visszaállítja egy virtuális gép lemezét, amely felhasználható egy új virtuális gép létrehozására.<br/><br/> Az Azure Backup biztosít egy sablont, amely segít a virtuális gépek testreszabásában és létrehozásában. <br/><br> A visszaállítási feladat létrehoz egy sablont, amely letölthető és használható egyéni virtuálisgép-beállítások megadásához, valamint virtuális gép létrehozásához.<br/><br/> A lemezek a megadott erőforráscsoportba vannak másolva.<br/><br/> Másik lehetőségként csatlakoztathatja a lemezt egy meglévő virtuális géphez, vagy létrehozhat egy új virtuális gépet a PowerShell használatával.<br/><br/> Ez a lehetőség olyankor hasznos, ha testre szeretné szabni a virtuális gépet, olyan konfigurációs beállításokat szeretne hozzáadni, amelyek a biztonsági másolat készítésekor még nem voltak meg, vagy olyan beállításokat szeretne megadni, amelyek a sablon vagy a PowerShell használatával konfigurálhatók.
**Meglévő cseréje** | Visszaállíthat egy lemezt, és lecserélheti vele a meglévő virtuális gépen lévő lemezt.<br/><br/> Az aktuális virtuális gépnek léteznie kell. Ha az már törölve lett, ez a lehetőség nem használható.<br/><br/> Azure Backup pillanatképet készít a meglévő virtuális gépről a lemez cseréje előtt, és a megadott előkészítési helyen tárolja azt. A virtuális képhez csatlakoztatott meglévő lemezek a kiválasztott visszaállítási ponttal lesznek felülírva.<br/><br/> A rendszer átmásolja a pillanatképet a tárolóba, és megőrzi a megőrzési szabályzatnak megfelelően. <br/><br/> A lemez cseréje művelet után az eredeti lemez megmarad az erőforráscsoportban. Dönthet úgy, hogy manuálisan törli az eredeti lemezeket, ha nincs rájuk szükség. <br/><br/>A meglévő cseréje nem titkosított felügyelt virtuális gépek esetén támogatott, beleértve az egyéni rendszerképekkel létrehozott [virtuális gépeket is.](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/) Klasszikus és nem felügyelet nélküli virtuális gépekhez nem támogatott.<br/><br/> Ha a visszaállítási pont több vagy kevesebb lemezzel rendelkezik, mint az aktuális virtuális gép, akkor a visszaállítási ponton lévő lemezek száma csak a virtuális gép konfigurációját fogja tükrözni.<br><br> A meglévő cseréje a virtuális gépek esetén [](../active-directory/managed-identities-azure-resources/overview.md) is támogatott társított erőforrásokkal, például felhasználó által hozzárendelt felügyelt identitással vagy [Key Vault.](../key-vault/general/overview.md)
**Régiók közötti (másodlagos régió)** | A régiók közötti visszaállítás az Azure-beli virtuális gépek visszaállítására használható a másodlagos régióban, amely egy [azure-beli párosított régió.](../best-practices-availability-paired-regions.md#what-are-paired-regions)<br><br> A kiválasztott helyreállítási pont összes Azure-beli virtuális gépét visszaállíthatja, ha a biztonsági mentés a másodlagos régióban történik.<br><br> A biztonsági mentés során a pillanatképek nem replikálódnak a másodlagos régióba. A rendszer csak a tárolóban tárolt adatokat replikálja. Így a másodlagos régió visszaállításai csak [tárolószint-visszaállítások.](about-azure-vm-restore.md#concepts) A másodlagos régió visszaállítási ideje majdnem ugyanaz lesz, mint az elsődleges régió tárolási rétegének visszaállítási ideje.  <br><br> Ez a funkció az alábbi lehetőségekhez érhető el:<br> <li> [Virtuális gép létrehozása](#create-a-vm) <br> <li> [Lemezek visszaállítása](#restore-disks) <br><br> A Meglévő lemezek cseréje lehetőség jelenleg nem [támogatott.](#replace-existing-disks)<br><br> Engedélyek<br> A másodlagos régió visszaállítási műveletét a biztonsági mentési rendszergazdák és az alkalmazás-rendszergazdák hajtják végre.

> [!NOTE]
> Egy Azure-beli virtuális gépen adott fájlokat és mappákat is helyreállíthat. [További információ](backup-azure-restore-files-from-vm.md).

## <a name="storage-accounts"></a>Tárfiókok

Néhány részlet a tárfiókokkal kapcsolatban:

- **Virtuális gép létrehozása:** Új virtuális gép létrehozásakor a virtuális gép a megadott tárfiókba kerül.
- **Lemez visszaállítása:** Lemez visszaállításakor a rendszer a lemezt a megadott tárfiókba másolja. A visszaállítási feladat létrehoz egy sablont, amely letölthető és használható egyéni virtuálisgép-beállítások megadásához. Ez a sablon a megadott tárfiókba kerül.
- **Lemez cseréje:** Ha egy meglévő virtuális gép egyik lemezét cseréli le, a Azure Backup pillanatképet készít a meglévő virtuális gépről a lemez cseréje előtt. A rendszer a pillanatképet is átmásolja a Recovery Services-tárolóba adatátvitelen keresztül, háttérfolyamatként. A pillanatkép-készítés fázisának befejezése után azonban a lemezek cseréje művelet aktiválódik. A lemez cseréje művelet után a forrás Azure-beli virtuális gép lemezei a művelethez megadott erőforráscsoportban maradnak, a VHD-ket pedig a megadott tárfiók tárolja. Dönthet úgy, hogy törli vagy megtartja ezeket a virtuális merevlemezeket és lemezeket.
- **Tárfiók helye:** A tárfióknak és a tárolónak ugyanabban a régióban kell lennie. Csak ezek a fiókok jelennek meg. Ha a helyen nincsenek tárfiókok, létre kell hoznia egyet.
- **Tároló típusa:** A Blob Storage nem támogatott.
- **Tárhelyredundania:** A zónaredundáns tárolás (ZRS) nem támogatott. A fiók replikációs és redundanciával kapcsolatos információi zárójelben jelennek meg a fiók neve után.
- **Premium Storage:**
  - A nem prémium szintű virtuális gépek visszaállításakor a Premium Storage-fiókok nem támogatottak.
  - Felügyelt virtuális gépek visszaállításakor a hálózati szabályokkal konfigurált prémium szintű tárfiókok nem támogatottak.

## <a name="before-you-start"></a>Előkészületek

A virtuális gép visszaállításához (új virtuális gép létrehozása) győződjön meg arról, hogy [](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) a megfelelő Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC) engedélyekkel rendelkezik a virtuális gép visszaállítására vonatkozó művelethez.

Ha nem rendelkezik engedélyekkel, visszaállíthat egy lemezt, majd [a](#restore-disks)lemez visszaállítása [](#use-templates-to-customize-a-restored-vm) után használhatja a visszaállítási művelet részeként létrehozott sablont egy új virtuális gép létrehozásához.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="select-a-restore-point"></a>Visszaállítási pont kiválasztása

1. A visszaállítani kívánt virtuális géphez társított tárolóban válassza a Biztonsági mentési **elemek**  >  **Azure-beli virtuális gép lehetőséget.**
1. Válasszon ki egy virtuális gépet. Alapértelmezés szerint a virtuális gép irányítópultján az elmúlt 30 nap helyreállítási pontjai jelennek meg. Megjeleníthet 30 napnál régebbi helyreállítási pontokat, vagy szűrhet, hogy a helyreállítási pontokat dátumok, időtartományok és különböző típusú pillanatkép-konzisztencia alapján keresse meg.
1. A virtuális gép visszaállításához válassza a Virtuális **gép visszaállítása lehetőséget.**

    ![Visszaállítási pont](./media/backup-azure-arm-restore-vms/restore-point.png)

1. Válassza ki a helyreállításhoz használni kívánt visszaállítási pontot.

## <a name="choose-a-vm-restore-configuration"></a>Virtuális gép visszaállítási konfigurációjának kiválasztása

1. A **Virtuális gép visszaállítása mezőben** válasszon visszaállítási lehetőséget:
    - **Új létrehozása:** Ezt a lehetőséget akkor használja, ha új virtuális gépet szeretne létrehozni. Létrehozhat egy virtuális gépet egyszerű beállításokkal, vagy visszaállíthat egy lemezt, és létrehozhat egy testreszabott virtuális gépet.
    - **Meglévő cseréje:** Akkor használja ezt a lehetőséget, ha egy meglévő virtuális gépen található lemezeket szeretne lecserélni.

        ![Virtuális gép konfigurációs varázslójának visszaállítása](./media/backup-azure-arm-restore-vms/restore-configuration.png)

1. Adja meg a kiválasztott visszaállítási beállítás beállításait.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

A egyik [visszaállítási lehetőségként](#restore-options)gyorsan létrehozhat egy virtuális gépet egy visszaállítási pontból származó alapszintű beállításokkal.

1. A **Virtuális gép visszaállítása** Új  >  **visszaállítási** típus létrehozása  >  **beállításnál** válassza a Virtuális gép létrehozása **lehetőséget.**
1. A **Virtuális gép neve területen** adjon meg egy virtuális gépet, amely nem létezik az előfizetésben.
1. Az **Erőforráscsoport területen** válasszon ki egy meglévő erőforráscsoportot az új virtuális géphez, vagy hozzon létre egy újat globálisan egyedi névvel. Ha már létező nevet rendel hozzá, az Azure a virtuális gép nevével azonos nevet rendel a csoporthoz.
1. A **Virtuális hálózat területen** válassza ki azt a virtuális hálózatot, amelybe a virtuális gépet helyezni fogja. Megjelenik az előfizetéshez társított összes virtuális hálózat. Válassza ki az alhálózatot. Alapértelmezés szerint az első alhálózat van kiválasztva.
1. Az **Átmeneti hely területen** adja meg a virtuális gép tárfiókját. [További információ](#storage-accounts).

    ![Konfigurációs varázsló visszaállítása – visszaállítási beállítások kiválasztása](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

1. Válassza **a Visszaállítás** lehetőséget a visszaállítási művelet aktiváláshoz.

## <a name="restore-disks"></a>Lemezek visszaállítása

A egyik [visszaállítási lehetőségként](#restore-options)létrehozhat egy lemezt egy visszaállítási pontból. Ezután a lemezzel a következő műveletek egyikét használhatja:

- Használja a visszaállítási művelet során létrehozott sablont a beállítások testreszabásához és a virtuális gépek üzembe helyezésének aktiválásához. Szerkessze az alapértelmezett sablonbeállításokat, és küldje el a sablont a virtuális gép üzembe helyezéséhez.
- [Visszaállított lemezek csatolása](../virtual-machines/windows/attach-managed-disk-portal.md) egy meglévő virtuális géphez.
- [Hozzon létre egy új virtuális](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks) gépet a visszaállított lemezekből a PowerShell használatával.

1. A **Konfiguráció visszaállítása Új**  >  **visszaállítási típus** létrehozása  >  **beállításnál** válassza a Lemezek visszaállítása **lehetőséget.**
1. Az **Erőforráscsoport mezőben** válasszon ki egy meglévő erőforráscsoportot a visszaállított lemezek számára, vagy hozzon létre egy újat globálisan egyedi névvel.
1. Az **Átmeneti hely beállításnál** adja meg azt a tárfiókot, amelybe a virtuális merevlemezeket másolni kell. [További információ](#storage-accounts).

    ![Erőforráscsoport és előkészítési hely kiválasztása](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

1. Válassza **a Visszaállítás** lehetőséget a visszaállítási művelet aktiváláshoz.

Ha a virtuális gép felügyelt lemezeket  használ, és a Virtuális gép létrehozása lehetőséget választja, Azure Backup a megadott tárfiókot nem használja. A Lemezek visszaállítása **és** az **Azonnali** visszaállítás esetén a tárfiók csak a sablon tárolására használható. A felügyelt lemezek a megadott erőforráscsoportban vannak létrehozva.
Ha a virtuális gép nem használt lemezeket használ, a rendszer blobként visszaállítja őket a tárfiókba.

### <a name="use-templates-to-customize-a-restored-vm"></a>Visszaállított virtuális gép testreszabása sablonok használatával

A lemez visszaállítása után használja a visszaállítási művelet részeként létrehozott sablont egy új virtuális gép testreszabásához és létrehozásához:

1. A **Biztonsági mentési feladatok részen** válassza ki a megfelelő visszaállítási feladatot.

1. A **Visszaállítás mezőben** válassza a **Sablon üzembe helyezése lehetőséget** a sablon üzembe helyezésének kezdeményezéséhez.

    ![Feladat-részletezés visszaállítása](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

1. A sablonban megadott virtuálisgép-beállítás testreszabásához válassza a **Sablon szerkesztése lehetőséget.** Ha további testreszabásokat szeretne hozzáadni, válassza a **Paraméterek szerkesztése lehetőséget.**
    - [További információ](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) az erőforrások egyéni sablonból való üzembe helyezéséről.
    - [További információ a](../azure-resource-manager/templates/template-syntax.md) sablonok írásról.

   ![Sablon üzembe helyezésének betöltése](./media/backup-azure-arm-restore-vms/edit-template1.png)

1. Adja meg a virtuális gép egyéni értékeit, fogadja el a **használati feltételeket,** majd válassza a **Vásárlás lehetőséget.**

   ![Sablon üzembe helyezésének elküldése](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="replace-existing-disks"></a>Meglévő lemezek cseréje

A egyik [visszaállítási lehetőségként](#restore-options)lecserélhet egy meglévő virtuálisgép-lemezt a kiválasztott visszaállítási pontra. [Tekintse át](#restore-options) az összes visszaállítási lehetőséget.

1. A **Konfiguráció visszaállítása mezőben** válassza a **Meglévő cseréje lehetőséget.**
1. A **Visszaállítás típusa mezőben** válassza a **Lemez/s cseréje lehetőséget.** Ez az a visszaállítási pont, amely a meglévő virtuálisgép-lemezek cseréjét fogja használni.
1. Az **Átmeneti hely beállításnál** adja meg, hogy a rendszer hová menti az aktuális felügyelt lemezek pillanatképét a visszaállítási folyamat során. [További információ](#storage-accounts).

   ![Konfigurációs varázsló visszaállítása Meglévő cseréje](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

## <a name="cross-region-restore"></a>Régiók közötti visszaállítás

A visszaállítási [](#restore-options)lehetőségek egyikeként a régiók közötti visszaállítás (CRR) lehetővé teszi az Azure-beli virtuális gépek visszaállítását egy másodlagos régióban, amely egy párosított Azure-régió.

A funkció használatának megkezdéséhez olvassa el [az Before You Begin (Kezdés előtt) című szakaszt.](./backup-create-rs-vault.md#set-cross-region-restore)

Ha meg kell tudni, hogy a CRR engedélyezve van-e, kövesse a régiók közötti [visszaállítás konfigurálási utasításait.](backup-create-rs-vault.md#configure-cross-region-restore)

### <a name="view-backup-items-in-secondary-region"></a>Másodlagos régióban lévő biztonsági mentési elemek megtekintése

Ha a CRR engedélyezve van, megtekintheti a másodlagos régióban lévő biztonsági mentési elemeket.

1. A portálon a **Recovery Services-tároló Biztonsági mentési** elemei  >  **elemhez elemet.**
1. A **másodlagos régió** elemeinek megtekintéséhez válassza a Másodlagos régió lehetőséget.

>[!NOTE]
>A listában csak a CRR funkciót támogató biztonságimásolat-kezelési típusok jelennek meg. Jelenleg csak a másodlagos régió adatainak másodlagos régióba való visszaállításának támogatása engedélyezett.<br></br>Az Azure-beli virtuális gépek crr-visszavonása támogatott az Azure-beli felügyelt virtuális gépeken (beleértve a titkosított Azure-beli virtuális gépeket is).

![Másodlagos régióban lévő virtuális gépek](./media/backup-azure-arm-restore-vms/secbackedupitem.png)

![Másodlagos régió kiválasztása](./media/backup-azure-arm-restore-vms/backupitems-sec.png)

### <a name="restore-in-secondary-region"></a>Visszaállítás másodlagos régióban

A másodlagos régió visszaállításának felhasználói élménye hasonló lesz az elsődleges régió-visszaállítási felhasználói felülethez. Amikor a Visszaállítási konfiguráció panelen konfigurálja a részleteket a visszaállítás konfigurálásához, a rendszer arra kéri, hogy csak másodlagos régióparamétereket adjon meg.

A másodlagos régió [RPO-ja](azure-backup-glossary.md#rpo-recovery-point-objective) jelenleg legfeljebb 12 óra az elsődleges régiótól, bár az írási hozzáférésű georedundáns tárolás [(RA-GRS)](../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) replikációja 15 perc.

![Válassza ki a visszaállítani választott virtuális gépet](./media/backup-azure-arm-restore-vms/sec-restore.png)

![Visszaállítási pont kiválasztása](./media/backup-azure-arm-restore-vms/sec-rp.png)

![Konfiguráció visszaállítása](./media/backup-azure-arm-restore-vms/rest-config.png)

![Folyamatban lévő visszaállítás aktiválása értesítés](./media/backup-azure-arm-restore-vms/restorenotifications.png)

- Virtuális gép visszaállításához és létrehozásához lásd: [Virtuális gép létrehozása.](#create-a-vm)
- A lemezként való visszaállításhoz tekintse meg a [Következőt: Lemezek visszaállítása.](#restore-disks)

>[!NOTE]
>
>- A visszaállítás aktiválása és az adatátviteli fázisban a visszaállítási feladat nem szakítható meg.
>- A régiók közötti visszaállítási funkció visszaállítja a CMK-t (ügyfél által felügyelt kulcsokat) engedélyező Azure-beli virtuális gépeket, amelyekről nem jön biztonsági mentés a CMK-kompatibilis Recovery Services-tárolóban, mivel a másodlagos régióban található, nem CMK-kompatibilis virtuális gépek.
>- A másodlagos régióban való visszaállításhoz szükséges Azure-szerepkörök ugyanazok, mint az elsődleges régióban.

[Az Azure-zónákhoz rögzített virtuális gépek](../virtual-machines/windows/create-portal-availability-zone.md) az azonos régió bármelyik rendelkezésre [állási](../availability-zones/az-overview.md) zónájában visszaállíthatóak.

A visszaállítási folyamat során a Rendelkezésre állási zóna **lehetőség látható.** Először az alapértelmezett zóna látható. Másik zóna választásához válassza ki a választott zóna számát. Ha a rögzített zóna nem érhető el, nem tudja visszaállítani az adatokat egy másik zónába, mert a biztonsági másolatok adatai nem lesznek zónázva replikálva. A rendelkezésre állási zónákban a visszaállítás csak a tárolószinten található helyreállítási pontokról lehetséges.

![Rendelkezésre állási zóna kiválasztása](./media/backup-azure-arm-restore-vms/cross-zonal-restore.png)

### <a name="monitoring-secondary-region-restore-jobs"></a>Másodlagos régió visszaállítási feladatának monitorozása

1. A portálon a **Recovery Services-tároló biztonsági** mentési  >  **feladatokhoz való ugrása**
1. A **másodlagos régió** elemeinek megtekintéséhez válassza a Másodlagos régió lehetőséget.

    ![Szűrt biztonsági mentési feladatok](./media/backup-azure-arm-restore-vms/secbackupjobs.png)

## <a name="restoring-unmanaged-vms-and-disks-as-managed"></a>Nem felügyelt virtuális gépek és lemezek visszaállítása felügyeltként

A rendszer lehetőséget biztosít a [](../storage/common/storage-disaster-recovery-guidance.md#azure-unmanaged-disks) nem felügyelt lemezek felügyelt lemezként való visszaállítására [a](../virtual-machines/managed-disks-overview.md) visszaállítás során. Alapértelmezés szerint a rendszer a nem nem által nem engedélyezett virtuális gépeket/lemezeket nem virtuális gépként/lemezként visszaállítja. Ha azonban felügyelt virtuális gépként/lemezként választja a visszaállítást, most már lehetséges. Ezek a visszaállítások nem a pillanatkép-fázisból aktiválódnak, hanem csak a tároló fázisból. Ez a funkció nem érhető el nem titkosított virtuális gépekhez.

![Visszaállítás felügyelt lemezként](./media/backup-azure-arm-restore-vms/restore-as-managed-disks.png)

## <a name="restore-vms-with-special-configurations"></a>Speciális konfigurációval konfigurált virtuális gépek visszaállítása

Számos gyakori forgatókönyv esetén szükség lehet a virtuális gépek visszaállítására.

**Forgatókönyv** | **Útmutató**
--- | ---
**Virtuális gépek visszaállítása a Hybrid Use Benefit használatával** | Ha egy Windows rendszerű virtuális gép a [Hybrid Use Benefit (HUB)](../virtual-machines/windows/hybrid-use-benefit-licensing.md)licenceléssel rendelkezik,  állítsa vissza a lemezeket, és hozzon létre egy új virtuális gépet a megadott sablonnal (a licenc típusa Windows_Server ), **vagy** a PowerShell használatával.  Ez a beállítás a virtuális gép létrehozása után is alkalmazható.
**Virtuális gépek visszaállítása Azure-adatközpont-katasztrófa esetén** | Ha a tároló GRS-t használ, és a virtuális gép elsődleges adatközpontja leáll, a Azure Backup támogatja a virtuális gépek biztonsági mentését a párosított adatközpontba. Kiválaszt egy tárfiókot a párosított adatközpontban, és a szokásos módon visszaállítja azt. Azure Backup a visszaállított virtuális gép létrehozásához a párosított régióban található számítási szolgáltatást használja. [További információ az](/azure/architecture/resiliency/recovery-loss-azure-region) adatközpontok rugalmasságáról.<br><br> Ha a tároló GRS-t használ, kiválaszthatja az új funkciót, a régiók közötti [visszaállítást.](#cross-region-restore) Ez lehetővé teszi a visszaállítást egy második régióba teljes vagy részleges kimaradás esetén, vagy akkor is, ha egyáltalán nem áll le.
**Operációs rendszer nélküli visszaállítás** | Az Azure-beli virtuális gépek és a helyszíni hipervizorok közötti fő különbség az, hogy az Azure-ban nem érhető el virtuálisgép-konzol. Bizonyos forgatókönyvekhez, például operációs rendszer nélküli helyreállítás (BMR) típusú biztonsági mentéssel való helyreállításhoz konzol szükséges. A virtuális gépek tárolóból való visszaállítása azonban teljesen helyettesíti a BMR-t.
**Speciális hálózati konfigurációval konfigurált virtuális gépek visszaállítása** | A speciális hálózati konfigurációk közé tartoznak a belső vagy külső terheléselosztást használó, több hálózati adaptert vagy több fenntartott IP-címet használó virtuális gépek. Ezeket a virtuális gépeket a Lemez visszaállítása [lehetőséggel lehet visszaállítani.](#restore-disks) Ez [a](../load-balancer/quickstart-load-balancer-standard-internal-powershell.md) beállítás másolatot hoz létre a VHD-kről a megadott tárfiókban, majd a konfigurációnak megfelelően létrehozhat egy belső vagy külső terheléselosztással, [](../virtual-machines/windows/multiple-nics.md)több hálózati adaptersel vagy több fenntartott [IP-címmel](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md)rendelkező virtuális gépet. [](../load-balancer/quickstart-load-balancer-standard-public-powershell.md)
**Hálózati biztonsági csoport (NSG) hálózati adapteren/alhálózaton** | Az Azure-beli virtuális gépek biztonsági mentése támogatja az NSG-információk biztonsági mentését és visszaállítását virtuális hálózat, alhálózat és hálózati adapter szintjén.
**Zóna által rögzített virtuális gépek** | Ha egy zónához rögzített Azure-beli virtuális gépről biztonsági mentést (Azure Backup), akkor ugyanabban a zónában is visszaállíthatja, ahol rögzítette. [További információ](../availability-zones/az-overview.md)
**Virtuális gép visszaállítása bármely rendelkezésre állási készletben** | Amikor a portálról állítja vissza a virtuális gépet, nincs lehetőség rendelkezésre állási készletet választani. A visszaállított virtuális gépek nem állnak rendelkezésre állási készletben. Ha a lemez visszaállítását választja, [](../virtual-machines/windows/tutorial-availability-sets.md) akkor a rendelkezésre állási csoport megadására akkor van lehetőség, amikor virtuális gépet hoz létre a lemezről a megadott sablon vagy a PowerShell használatával.
**Speciális virtuális gépek, például SQL virtuális gépek visszaállítása** | Ha azure-beli virtuális gép biztonsági mentésének használatával készít biztonsági másolatot egy SQL virtuális gépről, majd a virtuális gép visszaállítása lehetőséget használja, vagy virtuális gépet hoz létre a lemezek visszaállítása után, akkor az újonnan létrehozott virtuális gépet regisztrálni kell az SQL-szolgáltatónál az itt [említettek szerint.](../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md?tabs=azure-cli%2cbash) Ez a helyreállított virtuális gépet SQL virtuális gépké konvertálja.

### <a name="restore-domain-controller-vms"></a>Tartományvezérlő virtuális gépek visszaállítása

**Forgatókönyv** | **Útmutató**
--- | ---
**Egyetlen tartományvezérlő virtuális gép visszaállítása egyetlen tartományban** | Állítsa vissza a virtuális gépet, mint bármely más virtuális gépet. Vegye figyelembe:<br/><br/> A Active Directory szempontjából az Azure-beli virtuális gép hasonló a többi virtuális géphez.<br/><br/> A Címtárszolgáltatások helyreállító módja (DSRM) is elérhető, így minden Active Directory helyreállítási forgatókönyv használható. [További információ a](#post-restore-steps) virtualizált tartományvezérlők biztonsági mentésével és visszaállítási szempontjaival kapcsolatban.
**Több tartományvezérlő virtuális gép visszaállítása egyetlen tartományban** | Ha ugyanazon tartomány más tartományvezérlői is elérhetőek a hálózaton keresztül, a tartományvezérlő ugyanúgy visszaállítható, mint bármely más virtuális gép. Ha ez a tartomány utolsó fennmaradó tartományvezérlője, vagy ha elkülönített hálózatban végez helyreállítást, használjon [erdő-helyreállítást.](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)
**Egyetlen tartományvezérlő virtuális gép visszaállítása több tartományi konfigurációban** |  A lemezek visszaállítása és virtuális gép létrehozása a [PowerShell használatával](backup-azure-vms-automation.md#restore-the-disks)  
**Több tartomány visszaállítása egy erdőben** | Az erdő [helyreállítását javasoljuk.](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)

További információ: [Tartományvezérlők biztonsági Active Directory visszaállítása.](active-directory-backup-restore.md)

## <a name="track-the-restore-operation"></a>A visszaállítási művelet nyomon követése

A visszaállítási művelet aktiválása után a biztonsági mentési szolgáltatás létrehoz egy nyomkövetési feladatot. Azure Backup megjeleníti a feladatról szóló értesítéseket a portálon. Ha nem láthatók, válassza az  Értesítések szimbólumot,  majd válassza a További események lehetőséget a tevékenységnaplóban a Visszaállítási folyamat állapotának megtekintése érdekében.

![Visszaállítás aktiválva](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 A visszaállítás nyomon követése a következőképpen:

1. A feladat műveleteinek megtekintéséhez kattintson az értesítések hivatkozására. Másik lehetőségként a tárolóban válassza a **Biztonsági** mentési feladatok lehetőséget, majd válassza ki a megfelelő virtuális gépet.

    ![Tárolóban található virtuális gépek listája](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

1. A visszaállítási folyamat figyelése érdekében válasszon ki egy folyamatban állapotú **visszaállítási feladatot.** Ez megjeleníti a folyamatjelző sávot, amely a visszaállítási folyamat adatait jeleníti meg:

    - **A visszaállítás becsült ideje:** Kezdetben a visszaállítási művelet végrehajtásához szükséges időt biztosítja. A művelet előrehaladása során a visszaállítási művelet befejeződéskor a szükséges idő csökken, és nullára csökken.
    - **A visszaállítás százalékos aránya.** Megjeleníti a visszaállítási művelet százalékos arányát.
    - **Átvitt bájtok** száma: Ha egy új virtuális gép létrehozásával állítja vissza a visszaállítást, az átvitt bájtok számát jeleníti meg az átvitt bájtok teljes számával szemben.

## <a name="post-restore-steps"></a>Visszaállítás utáni lépések

A virtuális gépek visszaállítása után néhány dolgot érdemes megjegyezni:

- A biztonsági mentés konfigurálása során elérhető bővítmények telepítve vannak, de nincsenek engedélyezve. Ha problémát lát, telepítse újra a bővítményeket.
- Ha a virtuális gép biztonsági másolata statikus IP-címmel volt meg, akkor a visszaállított virtuális gép dinamikus IP-címmel fog foglalkozni az ütközések elkerülése érdekében. A [visszaállított virtuális géphez statikus IP-címet adhat.](/powershell/module/az.network/set-aznetworkinterfaceipconfig#description)
- A visszaállított virtuális gépek nem állnak rendelkezésre állási készletben. Ha a lemez visszaállítását választja, [](../virtual-machines/windows/tutorial-availability-sets.md) akkor a rendelkezésre állási csoport megadására akkor van lehetőség, amikor virtuális gépet hoz létre a lemezről a megadott sablon vagy a PowerShell használatával.
- Ha felhőbeli init-alapú Linux-disztribúciót használ, például Ubuntut, biztonsági okokból a jelszó le lesz tiltva a visszaállítás után. A visszaállított virtuális gépen használja a VMAccess bővítményt a [jelszó visszaállításához.](/troubleshoot/azure/virtual-machines/reset-password) Javasoljuk, hogy ezeken a disztribúciókon SSH-kulcsokat használ, így a visszaállítás után nem kell új jelszót megadnia.
- Ha a visszaállítás után nem tud hozzáférni egy virtuális géphez, mert megszakadt a kapcsolata a tartományvezérlővel, kövesse az alábbi lépéseket a virtuális gép telepítéséhez:
  - Csatlakoztassa az operációsrendszer-lemezt adatlemezként egy helyreállított virtuális géphez.
  - Ha az Azure-ügynök nem válaszol, manuálisan telepítse a virtuálisgép-ügynököt a következő hivatkozásra [kattintva:](/troubleshoot/azure/virtual-machines/install-vm-agent-offline).
  - Soros konzolos hozzáférés engedélyezése a virtuális gépen a virtuális gép parancssori hozzáférésének engedélyezéséhez

  ```cmd
    bcdedit /store <drive letter>:\boot\bcd /enum
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<<BOOT LOADER IDENTIFIER>>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```

  - A virtuális gép újraépítése Azure Portal helyi rendszergazdai fiók és jelszó alaphelyzetbe állításához
  - A Serial console hozzáférés és a CMD használata a virtuális gép tartományról való lecsatlakoztatáshoz

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```

- A virtuális gép lecsatlakozása és újraindítása után sikeresen rdP-kapcsolatot tud majd elérni a virtuális géphez helyi rendszergazdai hitelesítő adatokkal, és sikeresen újracsatlakoztathatja a virtuális gépet a tartományhoz.

## <a name="backing-up-restored-vms"></a>Visszaállított virtuális gépek biztonsági másolata

- Ha egy virtuális gépet az eredeti biztonsági mentést készítő virtuális gép nevével azonos nevű erőforráscsoportba visszaállított, a biztonsági mentés a visszaállítás után folytatódik a virtuális gépen.
- Ha egy másik erőforráscsoportba visszaállította a virtuális gépet, vagy másik nevet adott meg a visszaállított virtuális géphez, be kell állítania a biztonsági mentést a visszaállított virtuális géphez.

## <a name="next-steps"></a>Következő lépések

- Ha nehézségeket tapasztal a visszaállítási folyamat során, tekintse [át a](backup-azure-vms-troubleshoot.md#restore) gyakori problémákat és hibákat.
- A virtuális gép visszaállítása után ismerkedje meg a [virtuális gépek kezelésével](backup-azure-manage-vms.md)