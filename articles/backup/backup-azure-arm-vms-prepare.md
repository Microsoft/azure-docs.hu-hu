---
title: Azure-beli virtuális gépek biztonsági mentése egy Recovery Services-tárolóban
description: Ismerteti, hogyan lehet biztonsági másolatot készíteni az Azure-beli virtuális gépekről egy Recovery Services-tárolóban a Azure Backup használatával
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 5c3bc66c2111c347f8ed0e32c9e597a52ed404ed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670426"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Azure-beli virtuális gépek biztonsági mentése egy Recovery Services-tárolóban

Ez a cikk azt ismerteti, hogyan lehet biztonsági másolatot készíteni az Azure-beli virtuális gépekről egy Recovery Services-tárolóban a [Azure Backup](backup-overview.md) szolgáltatás használatával.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
>
> * Azure-beli virtuális gépek előkészítése.
> * Hozzon létre egy tárolót.
> * Virtuális gépek felderítése és biztonsági mentési szabályzat konfigurálása.
> * Azure-beli virtuális gépek biztonsági mentésének engedélyezése.
> * Futtatja a kezdeti biztonsági mentést.

> [!NOTE]
> Ez a cikk bemutatja, hogyan állíthat be egy tárolót, és válassza ki a virtuális gépeket a biztonsági mentéshez. Akkor hasznos, ha több virtuális gépről szeretne biztonsági mentést készíteni. Azt is megteheti, hogy [egy Azure-beli virtuális gép biztonsági mentését](backup-azure-vms-first-look-arm.md) közvetlenül a virtuális gép beállításaiból végezheti el.

## <a name="before-you-start"></a>Előkészületek

* [Tekintse át](backup-architecture.md#architecture-built-in-azure-vm-backup) az Azure virtuális gép biztonsági mentési architektúráját.
* [További](backup-azure-vms-introduction.md) információ Az Azure virtuális gépek biztonsági mentése és a biztonsági mentési bővítmény.
* A biztonsági mentés konfigurálása előtt [tekintse át a támogatási mátrixot](backup-support-matrix-iaas.md) .

Emellett van néhány dolog, amit bizonyos esetekben szükség lehet:

* **Telepítse a virtuálisgép-ügynököt a virtuális gépre**: Azure Backup biztonsági mentést készít az Azure-beli virtuális gépekről a számítógépen futó Azure VM-ügynök bővítményének telepítésével. Ha a virtuális gép Azure Piactéri rendszerképből lett létrehozva, akkor az ügynök telepítve van és fut. Ha egyéni virtuális gépet hoz létre, vagy egy helyszíni gépet telepít át, előfordulhat, hogy [manuálisan kell telepítenie az ügynököt](#install-the-vm-agent).

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

### <a name="modify-storage-replication"></a>Tárolási replikáció módosítása

Alapértelmezés szerint a [tárolók a Geo-redundáns tárolást (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage)használják.

* Ha a tároló elsődleges biztonsági mentési mechanizmusa, javasoljuk, hogy használja a GRS-t.
* A [helyileg redundáns tárolást (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) is használhatja egy olcsóbb megoldáshoz.
* A [Zone-redundáns tárolás (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage) a [rendelkezésre állási zónákban](../availability-zones/az-overview.md#availability-zones)replikálja az adatait, és biztosítja az adattárolást és a rugalmasságot ugyanabban a régióban.

Módosítsa a tárolási replikálás típusát a következőképpen:

1. Az új tárolóban válassza a **Tulajdonságok** lehetőséget a **Beállítások** szakaszban.
2. A **Tulajdonságok** alatt a **biztonsági mentés konfigurálása** területen válassza a **frissítés** lehetőséget.
3. Válassza ki a tárolási replikálás típusát, majd kattintson a **Mentés** gombra.

      ![Az új tároló tárolási konfigurációjának beállítása](./media/backup-azure-arm-vms-prepare/full-blade.png)

> [!NOTE]
   > A tárolási replikálási típus nem módosítható a tároló beállítása és a biztonsági mentési elemek létrehozása után. Ha ezt szeretné tenni, újra létre kell hoznia a tárolót.

## <a name="apply-a-backup-policy"></a>Biztonsági mentési szabályzat alkalmazása

Állítson be biztonsági mentési szabályzatot a tárolóhoz.

1. A tárolóban válassza a **+ biztonsági mentés** lehetőséget az **Áttekintés** szakaszban.

   ![Biztonsági mentés gomb](./media/backup-azure-arm-vms-prepare/backup-button.png)

1. A **biztonsági mentés célja**, hogy  >  **hol fut a munkaterhelés?** válassza az **Azure** lehetőséget. A **Miről szeretne biztonsági másolatot készíteni?** válassza a **virtuális gép**  >   **OK** elemet. Ezzel regisztrálja a virtuálisgép-bővítményt a tárolóban.

   ![Biztonsági mentési és biztonsági mentési célok ablaktáblái](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

1. A **biztonsági mentési házirendben** válassza ki a tárolóhoz társítandó szabályzatot.
    * Az alapértelmezett házirend naponta egyszer biztonsági másolatot készít a virtuális gépről. A napi biztonsági mentések 30 napig őrződnek meg. Az azonnali helyreállítási pillanatképeket két napig őrzi meg a rendszer.

      ![Alapértelmezett biztonsági mentési szabályzat](./media/backup-azure-arm-vms-prepare/default-policy.png)

    * Ha nem szeretné használni az alapértelmezett házirendet, válassza az **új létrehozása** lehetőséget, és hozzon létre egy egyéni szabályzatot a következő eljárásban leírtak szerint.

1. A **Virtual Machines** területen válassza a **Hozzáadás** lehetőséget.

      ![Virtuális gépek hozzáadása](./media/backup-azure-arm-vms-prepare/add-virtual-machines.png)

1. Ekkor megnyílik a **virtuális gépek kiválasztása** panel. Válassza ki azokat a virtuális gépeket, amelyekről biztonsági másolatot szeretne készíteni a szabályzat használatával. Ez után válassza az **OK** gombot.

   * A kiválasztott virtuális gépek ellenőrzése megtörtént.
   * Csak a tárolóval megegyező régióban lévő virtuális gépeket lehet kiválasztani.
   * A virtuális gépek csak egyetlen tárolóban készíthetők biztonsági mentésre.

     !["Virtuális gépek kiválasztása" panel](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

    >[!NOTE]
    > Az ugyanabban a régióban és előfizetésben lévő összes virtuális gép elérhető a biztonsági mentés konfigurálásához. A biztonsági mentés konfigurálásakor megkeresheti a virtuális gép nevét és az erőforráscsoportot, még akkor is, ha nem rendelkezik a szükséges engedélyekkel ezekhez a virtuális gépekhez. Ha a virtuális gép lágyan törölt állapotban van, akkor ez a lista nem jelenik meg. Ha újra kell védetté tenni a virtuális gépet, meg kell várnia, amíg a helyreállított törlési időszak lejár, vagy visszavonja a virtuális gép törlését a nem kötelezően törölt listából. További információkért lásd [a virtuális gépek Soft delete szolgáltatását ismertető cikket](soft-delete-virtual-machines.md#soft-delete-for-vms-using-azure-portal).

1. A **biztonsági mentés** területen válassza a **biztonsági mentés engedélyezése** lehetőséget. Ezzel telepíti a szabályzatot a tárolóba és a virtuális gépekre, és telepíti a biztonsági mentési bővítményt az Azure-beli virtuális gépen futó virtuálisgép-ügynökön.

A biztonsági mentés engedélyezése után:

* A Backup szolgáltatás telepíti a biztonsági mentési bővítményt, függetlenül attól, hogy a virtuális gép fut-e.
* A kezdeti biztonsági mentés a biztonsági mentési ütemterv szerint fog futni.
* A biztonsági mentések futtatásakor vegye figyelembe a következőket:
  * A-t futtató virtuális gépeknek a legnagyobb esélye van egy alkalmazás-konzisztens helyreállítási pont rögzítésére.
  * Azonban még ha a virtuális gép ki van kapcsolva, biztonsági mentés készül. Az ilyen virtuális gépek offline virtuális gép néven ismertek. Ebben az esetben a helyreállítási pont összeomlás-konzisztens lesz.
* Az Azure-beli virtuális gépek biztonsági mentésének engedélyezéséhez nem szükséges explicit kimenő kapcsolat.

### <a name="create-a-custom-policy"></a>Egyéni szabályzat létrehozása

Ha új biztonsági mentési szabályzatot jelölt ki, töltse ki a házirend-beállításokat.

1. A **Házirend neve** mezőben adjon meg egy értelmes nevet.
2. A **biztonsági mentés ütemezése** területen határozza meg, hogy a biztonsági mentés mikor történjen. Az Azure-beli virtuális gépek napi vagy heti biztonsági mentését is elvégezheti.
3. Az **azonnali visszaállítás** mezőben adja meg, hogy az azonnali visszaállításhoz milyen hosszan szeretné megőrizni a pillanatképeket helyileg.
    * A visszaállításkor a rendszer átmásolja a virtuális gép lemezeit a tárterületről a hálózatról a helyreállítási tároló helyére. Az azonnali visszaállítással a biztonsági mentési feladatok során készített helyileg tárolt pillanatképeket is kihasználhatja, anélkül, hogy a biztonsági mentési adatok átvitelére kellene várnia a tárolóba.
    * Az azonnali visszaállításhoz a pillanatképeket egy-öt nap alatt megtarthatja. Két nap az alapértelmezett beállítás.
4. A **megőrzési tartomány** mezőben adja meg, hogy mennyi ideig szeretné megőrizni a napi vagy heti biztonsági mentési pontokat.
5. A **havi biztonsági mentési pontok megőrzése** és **az éves biztonsági mentési pontok megőrzése** érdekében adja meg, hogy a napi vagy heti biztonsági mentések havi vagy éves biztonsági mentését kívánja-e megőrizni.
6. A szabályzat mentéséhez kattintson **az OK gombra** .
    > [!NOTE]
    > A Restore Point-gyűjtemény (RPC) tárolásához a Backup szolgáltatás egy külön erőforráscsoportot (RG) hoz létre. Ez a RG különbözik a virtuális gép RG-től. [További információ](backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines).

    ![Új biztonsági mentési szabályzat](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > A Azure Backup nem támogatja az automatikus óra-beállítást az Azure-beli virtuális gépek biztonsági mentésének napfényes változásaihoz. Az idő változásakor a biztonsági mentési szabályzatokat szükség szerint manuálisan kell módosítani.

## <a name="trigger-the-initial-backup"></a>A kezdeti biztonsági mentés elindítása

A kezdeti biztonsági mentés az ütemterv szerint fog futni, de az alábbiak szerint azonnal futtatható:

1. A tároló menüben válassza a **biztonsági másolati elemek elemet**.
2. A **biztonsági másolati elemek** területen válassza az Azure-beli **virtuális gép** lehetőséget.
3. A **biztonsági mentési elemek** listában válassza a három pontot (...).
4. Válassza a **biztonsági mentés** lehetőséget.
5. A **biztonsági mentés most** a Calendar (naptár) vezérlőelem használatával válassza ki azt az utolsó napot, ameddig a helyreállítási pontot meg kell őrizni. Ez után válassza az **OK** gombot.
6. A portál értesítéseinek figyelése. A feladat előrehaladását a tároló irányítópultján követheti nyomon > **biztonsági mentési feladatok**  >  **folyamatban** vannak. A virtuális gép méretétől függően a kezdeti biztonsági mentés létrehozása hosszabb időt vehet igénybe.

## <a name="verify-backup-job-status"></a>Biztonsági mentési feladatok állapotának ellenőrzése

Az egyes virtuális gépek biztonsági mentési feladatának részletei két fázisból állnak, a **Pillanatkép** fázisát, majd az **adatok átvitele a tároló** fázisát.<br/>
A pillanatkép-fázis garantálja egy helyreállítási pont rendelkezésre állását az **azonnali** helyreállításhoz használt lemezekkel együtt, és a felhasználó által konfigurált pillanatkép-megőrzéstől függően legfeljebb öt napig elérhető. Az adatok tárolóba való átvitele egy helyreállítási pontot hoz létre a tárban a hosszú távú megőrzés érdekében. Az adatok a tárba való átvitele csak a pillanatkép-fázis befejeződése után kezdődik.

  ![Biztonsági mentési feladatok állapota](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

Két **Alfeladat** fut a háttérben, egyet az előtér-biztonsági mentési feladathoz, amelyet a **biztonsági mentési feladat** részletei panelről az alábbi módon lehet ellenőrizni:

  ![Biztonsági mentési feladat állapotának alárendelt feladatai](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

Az **adatok átvitele a tárba** fázisba több napot is igénybe vehet, a lemezek méretétől, a lemeztől és számos más tényezőtől függően.

A feladatok állapota a következő esetektől függően változhat:

**Pillanatkép** | **Adatok átvitele a tárba** | **Feladatok állapota**
--- | --- | ---
Befejeződött | Folyamatban | Folyamatban
Befejeződött | Kimarad | Befejeződött
Befejeződött | Befejeződött | Befejeződött
Befejeződött | Sikertelen | Figyelmeztetéssel fejeződött be
Sikertelen | Sikertelen | Sikertelen

Ezzel a képességgel ugyanezen a virtuális gépen két biztonsági mentés futtatható párhuzamosan, de mindkét fázisban (pillanatkép, adatok átvitele a tárba) csak egy Alfeladat futhat. Így olyan helyzetekben, amikor egy folyamatban lévő biztonsági mentési feladat a következő nap biztonsági mentését eredményezte, a rendszer ezt a leválasztási funkciót fogja elkerülni. A következő napok biztonsági mentései a pillanatképet elvégezték, az **adatok a tárolóba való átvitele** pedig kimarad, ha egy korábbi nap biztonsági mentési feladata folyamatban van.
A tárolóban létrehozott növekményes helyreállítási pont rögzíti a tárolóban létrehozott legutóbbi helyreállítási pont összes változását. Nincs hatással a felhasználóra.

## <a name="optional-steps"></a>Nem kötelező lépések

### <a name="install-the-vm-agent"></a>A virtuálisgép-ügynök telepítése

Azure Backup biztonsági mentést készít az Azure-beli virtuális gépekről a számítógépen futó Azure virtuálisgép-ügynök bővítményének telepítésével. Ha a virtuális gép Azure Piactéri rendszerképből lett létrehozva, akkor az ügynök telepítve van és fut. Ha egyéni virtuális gépet hoz létre, vagy egy helyszíni gépet telepít át, előfordulhat, hogy manuálisan kell telepítenie az ügynököt a táblázatban foglaltak szerint.

**VM** | **Részletek**
--- | ---
**Windows** | 1. [töltse le és telepítse](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) az ügynök MSI-fájlját.<br/><br/> 2. telepítsen rendszergazdai engedélyekkel a gépen.<br/><br/> 3. Ellenőrizze a telepítést. A virtuális gép *C:\WindowsAzure\Packages* kattintson a jobb gombbal **WaAppAgent.exe**  >  **Tulajdonságok** elemre. A **részletek** lapon a **termék verziószámának** 2.6.1198.718 vagy magasabbnak kell lennie.<br/><br/> Ha frissíti az ügynököt, győződjön meg arról, hogy nem fut biztonsági mentési művelet, majd [telepítse újra az ügynököt](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | A telepítést egy RPM vagy egy DEB-csomag használatával telepítheti a terjesztési csomag adattárában. Ez az Azure Linux-ügynök telepítésének és frissítésének előnyben részesített módszere. Az összes [támogatott terjesztési szolgáltató](../virtual-machines/linux/endorsed-distros.md) integrálja az Azure Linux-ügynök csomagját a lemezképbe és a tárházba. Az ügynök elérhető a [githubon](https://github.com/Azure/WALinuxAgent), de nem javasoljuk, hogy innen telepítsen.<br/><br/> Ha frissíti az ügynököt, győződjön meg arról, hogy nem fut biztonsági mentési művelet, és frissítse a bináris fájlokat.

## <a name="next-steps"></a>Következő lépések

* Az [Azure VM-ügynökökkel](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) vagy az [Azure-beli virtuális gépek biztonsági mentésével](backup-azure-vms-troubleshoot.md)kapcsolatos problémák elhárítása.
* [Visszaállítás](backup-azure-arm-restore-vms.md) Azure-beli virtuális gépek.