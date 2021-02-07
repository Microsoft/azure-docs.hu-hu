---
title: Az Azure Disk Backup támogatási mátrixa
description: Összefoglalja a támogatási beállításokat és az Azure Disk Backup korlátozásait.
ms.topic: conceptual
ms.date: 01/07/2021
ms.custom: references_regions
ms.openlocfilehash: 7d6de863a45bcadd73e847cbf407c13363952151
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806104"
---
# <a name="azure-disk-backup-support-matrix-in-preview"></a>Az Azure Disk Backup támogatási mátrixa (előzetes verzió)

>[!IMPORTANT]
>Az Azure Disk Backup előzetes verzióban érhető el, és nem ajánlott éles környezetben üzemelő számítási feladatokhoz. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
>[Töltse ki ezt a kérdőívet](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) az előzetes verzióra való feliratkozáshoz.

Az Azure-lemezek védelme [Azure Backup](./backup-overview.md) használatával biztosítható. Ez a cikk a régiók rendelkezésre állását, a támogatott forgatókönyveket és korlátozásokat foglalja össze.

## <a name="supported-regions"></a>Támogatott régiók

Az Azure Disk Backup előzetes verzióban érhető el a következő régiókban: USA nyugati régiója, USA nyugati középső régiója, Kelet-RÉGIÓJA, Közép-Kanada, Egyesült Királyság nyugati régiója, Észak-Svájc, Nyugat-Svájc, Ausztrália középső régiója, Ausztrália középső régiója, Korea középső régiója, Dél-Korea, Japán Kelet-Ázsia nyugati régiója, Egyesült Arab Emírségek, Dél-Brazília, Közép-India 

További régiók lesznek bejelentve, amikor elérhetővé válnak.

## <a name="limitations"></a>Korlátozások

- Az Azure lemezes biztonsági mentést az Azure Managed Disks támogatja, beleértve a megosztott lemezeket is (közös prémium SSD-k). A nem felügyelt lemezek nem támogatottak. Ez a megoldás jelenleg nem támogatja az ultra-lemezeket, beleértve a megosztott Ultra-lemezeket, a pillanatkép-képesség hiánya miatt.

- Az Azure Disk Backup támogatja írásgyorsító lemez biztonsági mentését. A visszaállítás során azonban a lemez normál lemezként lesz helyreállítva. Az írási gyorsított gyorsítótár a virtuális géphez való csatlakoztatást követően engedélyezhető a lemezen.

- A Azure Backup naponta több biztonsági mentést támogató Azure Managed Disks működési (pillanatkép-) rétegét biztosítja. A biztonsági mentések nem másolódnak át a Backup-tárolóba.

- Jelenleg a Original-Location helyreállítási (OLR) lehetőség a visszaállításhoz a meglévő forrásfájlokat lecserélve, ahonnan a biztonsági mentések nem támogatottak. Visszaállíthatja a helyreállítási pontról, hogy egy új lemezt hozzon létre ugyanabba az erőforráscsoporthoz, amelyből a biztonsági másolatokat, illetve a biztonsági mentéseket vagy más erőforráscsoportot. Ez a Alternate-Location Recovery (ALR) néven ismert.

- A Managed Disks Azure Backup növekményes pillanatképeket használ, amelyek lemezenként legfeljebb 200 pillanatképre korlátozódnak. Annak érdekében, hogy az ütemezett biztonsági mentéstől eltekintve igény szerinti biztonsági mentést készítsen, a biztonsági mentési szabályzat a 180-re korlátozza a teljes biztonsági mentést. További információ a felügyelt lemezek [növekményes pillanatképéről](../virtual-machines/disks-incremental-snapshots.md#restrictions) .

- Az Azure [-előfizetések és-szolgáltatások korlátozásai](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machine-disk-limits) régiónként/előfizetéshez tartozó lemezes pillanatképek teljes száma esetén érvényesek.

- A virtuális géphez csatlakoztatott több lemez időpontra vonatkozó pillanatképei nem támogatottak.

- A Backup-tároló és a biztonsági mentésre szolgáló lemezek lehetnek azonos vagy eltérő előfizetésekben. Ugyanakkor a Backup-tárolónak és a biztonsági mentéshez használt lemeznek azonos régióban kell lennie.

- Azok a lemezek, amelyekről biztonsági mentést kell készíteni, és az a pillanatkép-erőforráscsoport, amelyben a pillanatképek helyileg tárolódnak, azonos előfizetésben kell lenniük.

- A lemez biztonsági másolatból ugyanabba vagy egy másik előfizetésbe való visszaállítása támogatott. A visszaállított lemez azonban ugyanabban a régióban lesz létrehozva, mint a pillanatkép.

- Az ADE titkosított lemezeket nem lehet védeni.

- A platform által felügyelt kulcsokkal vagy az ügyfél által felügyelt kulcsokkal titkosított lemezek támogatottak. A visszaállítás azonban sikertelen lesz az ügyfél által felügyelt kulcsokkal (CMK) titkosított lemezek visszaállítási pontjai esetében, ha a lemez titkosítási készletének kulcstartója le van tiltva vagy törölve van.

- A biztonsági mentési szabályzat jelenleg nem módosítható, és a biztonságimásolat-készítési példányhoz rendelt pillanatkép-erőforráscsoport nem módosítható a lemez biztonsági mentésének konfigurálásakor.

- Jelenleg a lemezek biztonsági mentésének konfigurálásához szükséges Azure Portal-élmény az azonos előfizetésből legfeljebb 20 lemezre korlátozódik.

- Jelenleg (az előzetes verzió alatt) a PowerShell és az Azure CLI használata a lemezek biztonsági mentésének és visszaállításának konfigurálásához nem támogatott.

- A biztonsági mentés konfigurálásakor a rendszer biztonsági mentésre kijelölt lemezt és a pillanatképek tárolására szolgáló pillanatkép-erőforráscsoportot ugyanahhoz az előfizetéshez kell tartoznia. Nem hozható létre növekményes pillanatkép a lemez előfizetésén kívüli adott lemezhez. További információ a felügyelt lemez [növekményes pillanatképekről](../virtual-machines/windows/disks-incremental-snapshots-portal.md#restrictions) . A pillanatképek erőforráscsoport kiválasztásával kapcsolatos további információkért lásd:  [biztonsági mentés konfigurálása](backup-managed-disks.md#configure-backup).

- A sikeres biztonsági mentési és visszaállítási műveletek esetén a biztonsági mentési tár felügyelt identitásához szerepkör-hozzárendelésekre van szükség. Csak a dokumentációban megadott szerepkör-definíciókat használja. A más szerepkörök (például a tulajdonos, a közreműködők stb.) használata nem támogatott. Jogosultsági problémák merülhetnek fel, ha a szerepkörök kiosztása után hamarosan elindítja a biztonsági mentési vagy visszaállítási műveletek konfigurálását. Ennek az az oka, hogy a szerepkör-hozzárendelések érvénybe léptetése néhány percet vesz igénybe.

- A felügyelt lemezek lehetővé teszik a teljesítményi szint módosítását a telepítés során, vagy később, a lemez méretének módosítása nélkül. Az Azure lemezes biztonsági mentési megoldás a biztonsági mentés alatt álló forrás lemezének változásait támogatja. A visszaállítás során a visszaállított lemez teljesítményi szintje megegyezik a biztonsági mentés időpontjában lévő forrásoldali lemezzel. Ha a visszaállítási művelet után módosítani szeretné a lemez teljesítményének [szintjét, kövesse](../virtual-machines/disks-performance-tiers-portal.md) a dokumentációt.

- A felügyelt lemezek [privát hivatkozásai](../virtual-machines/disks-enable-private-links-for-import-export-portal.md) lehetővé teszik a felügyelt lemezek exportálásának és importálásának korlátozását úgy, hogy az csak az Azure-beli virtuális hálózaton belül történjen. Az Azure Disk Backup támogatja a privát végpontokkal rendelkező lemezek biztonsági mentését. Ez nem tartalmazza a privát végponton keresztül elérhető biztonsági mentési vagy Pillanatképek elérését.

- Az előzetes verzió ideje alatt nem tilthatja le a biztonsági mentést, így a biztonsági mentés **leállítása és a biztonsági mentési adat megőrzése** nem támogatott. Törölhet egy biztonsági mentési példányt, amely nem csak leállítja a biztonsági mentést, hanem törli az összes biztonsági mentési adatkészletet is.

## <a name="next-steps"></a>Következő lépések

- [Azure-Managed Disks biztonsági mentése](backup-managed-disks.md)
