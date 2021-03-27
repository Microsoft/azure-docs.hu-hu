---
title: Az Azure Disk Backup támogatási mátrixa
description: Összefoglalja a támogatási beállításokat és az Azure Disk Backup korlátozásait.
ms.topic: conceptual
ms.date: 01/07/2021
ms.custom: references_regions
ms.openlocfilehash: 88ec26837cc8f69c1e84c77ea6b57ce16e462e0a
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612857"
---
# <a name="azure-disk-backup-support-matrix"></a>Az Azure Disk Backup támogatási mátrixa

Az Azure-lemezek védelme [Azure Backup](./backup-overview.md) használatával biztosítható. Ez a cikk a régiók rendelkezésre állását, a támogatott forgatókönyveket és korlátozásokat foglalja össze.

## <a name="supported-regions"></a>Támogatott régiók

Az Azure Disk Backup a következő régiókban érhető el: USA nyugati régiója, USA 2. nyugati régiója, USA nyugati középső régiója, USA keleti régiója, Kelet-RÉGIÓJA, USA középső régiója, az USA déli középső régiója, az USA északi középső régiója, Észak-európa, közép-Brazília, Dél-Afrika északi régió Középnyugat-Németország Nyugat-Svájc Észak-Svájcja, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója, Nyugat-Európa, Észak-Ausztrália, Közép-Ausztrália , Kelet-Ausztrália, Korea középső régiója, Dél-Korea, Kelet-Japán, Nyugat-Japán, Kelet-Ázsia, Délkelet-Ázsia, Közép-India. 

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

- Az Azure Disk Backup támogatja a PowerShellt. Az Azure CLI jelenleg nem támogatott.

- A biztonsági mentés konfigurálásakor a rendszer biztonsági mentésre kijelölt lemezt és a pillanatképek tárolására szolgáló pillanatkép-erőforráscsoportot ugyanahhoz az előfizetéshez kell tartoznia. Nem hozható létre növekményes pillanatkép a lemez előfizetésén kívüli adott lemezhez. További információ a felügyelt lemez [növekményes pillanatképekről](../virtual-machines/disks-incremental-snapshots.md#restrictions) . A pillanatképek erőforráscsoport kiválasztásával kapcsolatos további információkért lásd:  [biztonsági mentés konfigurálása](backup-managed-disks.md#configure-backup).

- A sikeres biztonsági mentési és visszaállítási műveletek esetén a biztonsági mentési tár felügyelt identitásához szerepkör-hozzárendelésekre van szükség. Csak a dokumentációban megadott szerepkör-definíciókat használja. A más szerepkörök (például a tulajdonos, a közreműködők stb.) használata nem támogatott. Jogosultsági problémák merülhetnek fel, ha a szerepkörök kiosztása után hamarosan elindítja a biztonsági mentési vagy visszaállítási műveletek konfigurálását. Ennek az az oka, hogy a szerepkör-hozzárendelések érvénybe léptetése néhány percet vesz igénybe.

- A felügyelt lemezek lehetővé teszik a teljesítményi szint módosítását a telepítés során, vagy később, a lemez méretének módosítása nélkül. Az Azure lemezes biztonsági mentési megoldás a biztonsági mentés alatt álló forrás lemezének változásait támogatja. A visszaállítás során a visszaállított lemez teljesítményi szintje megegyezik a biztonsági mentés időpontjában lévő forrásoldali lemezzel. Ha a visszaállítási művelet után módosítani szeretné a lemez teljesítményének [szintjét, kövesse](../virtual-machines/disks-performance-tiers-portal.md) a dokumentációt.

- A felügyelt lemezek [privát hivatkozásai](../virtual-machines/disks-enable-private-links-for-import-export-portal.md) lehetővé teszik a felügyelt lemezek exportálásának és importálásának korlátozását úgy, hogy az csak az Azure-beli virtuális hálózaton belül történjen. Az Azure Disk Backup támogatja a privát végpontokkal rendelkező lemezek biztonsági mentését. Ez nem tartalmazza a privát végponton keresztül elérhető biztonsági mentési vagy Pillanatképek elérését.

- Törölhet egy biztonsági mentési példányt, amely leállítja a biztonsági mentést, és törli az összes biztonsági mentési adatkészletet is. Jelenleg nem lehet letiltani a biztonsági mentést, mert a biztonsági mentés **leállítása és a biztonsági mentési adat megőrzése** beállítás nem támogatott.

## <a name="next-steps"></a>Következő lépések

- [Azure-Managed Disks biztonsági mentése](backup-managed-disks.md)