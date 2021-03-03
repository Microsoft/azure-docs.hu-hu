---
title: Tudnivalók az Azure-beli virtuális gépek visszaállítási folyamatáról
description: Ismerje meg, hogyan állítja vissza az Azure Virtual Machines szolgáltatást a Azure Backup szolgáltatás
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 62d1ff7973693f29c77c77fe2ad4fbbb598a5fa4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738412"
---
# <a name="about-azure-vm-restore"></a>Azure-beli virtuális gépek visszaállítása

Ez a cikk azt ismerteti, hogyan állítja vissza az Azure Virtual Machines (VM) [szolgáltatást a Azure Backup szolgáltatás](./backup-overview.md) . Számos visszaállítási lehetőség van. Megbeszéljük az általuk támogatott különböző forgatókönyveket.

## <a name="concepts"></a>Alapelvek

- **Helyreállítási pont** (más néven **visszaállítási pont**): a helyreállítási pont az eredeti, biztonsági mentés alatt álló adatbázis másolata.

- **Szint (pillanatkép** és tár): az Azure virtuális gép biztonsági mentése két fázisban történik:

  - Az 1. fázisban az elvégzett pillanatkép a lemezzel együtt tárolódik. Ezt a **Pillanatkép-szintet** nevezzük. A pillanatkép-csomagok visszaállítása gyorsabb (mint a tárolóból történő visszaállítás), mert a pillanatképek a visszaállítás elindítása előtt nem tudják megvárni a pillanatképek másolását a tárba. Így a pillanatképek szintjéről történő visszaállítást [azonnali visszaállításnak](./backup-instant-restore-capability.md)is nevezik.
  - A 2. fázisban a pillanatkép átadása és tárolása a Azure Backup szolgáltatás által felügyelt tárolóban történik. Ezt a tárolói **szintet** nevezzük.

- **Eredeti helyre történő helyreállítás (OLR)**: a visszaállítási pontról a forrás Azure virtuális gépre, ahonnan a biztonsági másolatok készültek, a helyreállítási pontban tárolt állapotra cserélve. Ez váltja fel a forrás virtuális gép operációsrendszer-lemezét és adatlemezét.

- **Alternatív hely helyreállítása (ALR)**: a helyreállítási pontról egy olyan kiszolgálóra történt helyreállítás, amely nem az eredeti kiszolgáló, ahol a biztonsági másolatok készültek.

- **Elemszintű visszaállítás (ILR):** A virtuális gépen lévő egyes fájlok vagy mappák visszaállítása a helyreállítási pontról

- **Rendelkezésre állás (replikációs típusok)**: a Azure Backup kétféle replikációt biztosít a tárterület/az adatmennyiség nagyfokú elérhetőségének biztosításához:
  - A [helyileg redundáns tárolás (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) háromszor replikálja az adatait (az adatait három példányban hozza létre) egy adatközpont tárolási méretezési egységében. Az adatok összes másolata ugyanabban a régióban található. Az LRS egy alacsony költségű megoldás az adatok védelmére a helyi hardveres hibák esetén.
  - A [geo-redundáns tárolás (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage) az alapértelmezett és ajánlott replikációs lehetőség. A GRS az adatait egy másodlagos régióba replikálja (több száz kilométerre a forrásadatok elsődleges helyétől). A GRS több mint LRS, de a GRS magasabb szintű tartósságot biztosít az adataihoz, még akkor is, ha van regionális leállás.
  - A [Zone-redundáns tárolás (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage) a [rendelkezésre állási zónákban](../availability-zones/az-overview.md#availability-zones)replikálja az adatait, és biztosítja az adattárolást és a rugalmasságot ugyanabban a régióban. A ZRS nem rendelkezik állásidővel. Így az [adattárolást](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/)igénylő kritikus fontosságú munkaterhelések, valamint az állásidő nélkül is készíthető biztonsági mentés a ZRS-ben.

- **Régiók közötti visszaállítás (CRR)**: a [visszaállítási lehetőségek](./backup-azure-arm-restore-vms.md#restore-options)egyike a tartományok közötti visszaállítás (CRR) lehetővé teszi az Azure-beli virtuális gépek visszaállítását egy másodlagos régióba, amely egy [Azure párosított régió](../best-practices-availability-paired-regions.md#what-are-paired-regions) , amely bármikor visszaállíthatja adatait a másodlagos régióban, a részleges vagy teljes kimaradások során, vagy bármilyen más időpontban. 

## <a name="restore-scenarios"></a>Visszaállítási forgatókönyvek

![Visszaállítási forgatókönyvek ](./media/about-azure-vm-restore/recovery-scenarios.png)

| **Forgatókönyv**                                                 | **Mi történik**                                             | **A következő esetekben használja**                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Visszaállítás új virtuális gép létrehozásához](./backup-azure-arm-restore-vms.md) | Visszaállítja a teljes virtuális gépet a OLR (ha a forrás virtuális gép még létezik) vagy ALR | <li> Ha a forrásként szolgáló virtuális gép elveszett vagy sérült, akkor teljes virtuális gépet állíthat vissza.  <li> Létrehozhat egy másolatot a virtuális gépről  <li> A naplózási és megfelelőségi műveletek visszaállítására is lehetőség van  <li> Ez a lehetőség nem működik a Piactéri rendszerképekből létrehozott Azure-beli virtuális gépek esetén (azaz ha nem érhetők el, mert a licenc lejárt). |
| [A virtuális gép lemezei visszaállítása](./backup-azure-arm-restore-vms.md#restore-disks) | Virtuális géphez csatlakoztatott lemezek visszaállítása                             |  Összes lemez: Ez a beállítás létrehozza a sablont, és visszaállítja a lemezt. A sablont speciális konfigurációkkal (például a rendelkezésre állási csoportokkal) szerkesztheti, hogy megfeleljen a követelményeknek, majd a sablon használatával és a lemez visszaállításával hozza létre újra a virtuális gépet. |
| [Virtuális gépen belüli adott fájlok visszaállítása](./backup-azure-restore-files-from-vm.md) | Válassza a visszaállítási pont lehetőséget, tallózással válassza ki a fájlokat, majd állítsa vissza őket ugyanarra a (vagy kompatibilis) operációs rendszerre, mint a biztonsági másolattal elválasztott virtuális gép. |  Ha ismeri a visszaállítani kívánt fájlokat, akkor a teljes virtuális gép visszaállítása helyett használja ezt a lehetőséget. |
| [Titkosított virtuális gép visszaállítása](./backup-azure-vms-encryption.md) | A portálról állítsa vissza a lemezeket, majd a PowerShell használatával hozza létre a virtuális gépet. | <li> [Titkosított virtuális gép Azure Active Directory](../virtual-machines/windows/disk-encryption-windows-aad.md)  <li> [Titkosított virtuális gép Azure AD nélkül](../virtual-machines/windows/disk-encryption-windows.md) <li> [Titkosított virtuális gép *Az Azure ad-vel* az Azure ad-vel való Migrálás *nélkül*](../virtual-machines/windows/disk-encryption-faq.md#can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app) |
| [Régiók közötti visszaállítás](./backup-azure-arm-restore-vms.md#cross-region-restore) | Új virtuális gép létrehozása vagy lemezek visszaállítása másodlagos régióba (Azure párosított régió) | <li> **Teljes leállás**: a régiók közötti visszaállítási szolgáltatással a másodlagos régióban nem lehet várni az adatok helyreállítására. A visszaállításokat a másodlagos régióban is kezdeményezheti, még az Azure előtt is. <li> **Részleges leállás**: az állásidő olyan tárolási fürtökön fordulhat elő, ahol a Azure Backup tárolja a biztonsági másolatok adatait, vagy akár hálózatban is, összekapcsolva a biztonsági másolatban szereplő adataihoz társított Azure Backup és Storage-fürtöket. A régiók közötti visszaállítással a másodlagos régióban visszaállíthat egy visszaállítást a másodlagos régióban. <li> **Nincs leállás**: az üzletmenet-folytonosságot és a vész-helyreállítási (BCDR) gyakorlatokat a másodlagos régió adataival történő auditálási és megfelelőségi célokra is elvégezheti. Ez lehetővé teszi a biztonsági másolatok adatainak visszaállítását a másodlagos régióban, még akkor is, ha az elsődleges régióban nincs teljes vagy részleges leállás az üzletmenet folytonosságának és a vész-helyreállítási gyakorlatoknak.  |

## <a name="next-steps"></a>Következő lépések

- [Gyakori kérdések a virtuális gépek visszaállításáról](/azure/backup/backup-azure-vm-backup-faq#restore)
- [Támogatott visszaállítási módszerek](./backup-support-matrix-iaas.md#supported-restore-methods)
- [Visszaállítási problémák elhárítása](./backup-azure-vms-troubleshoot.md#restore)
