---
title: Biztonsági másolatok kezelése az Azure szerepköralapú hozzáférés-vezérléssel
description: Az Azure szerepköralapú hozzáférés-vezérlés használatával kezelheti Recovery Services-tárolóban a biztonsági mentési kezelési műveletekhez való hozzáférést.
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 179cb6efcff4bcf50a64a6d58f861622e853b02b
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553408"
---
# <a name="use-azure-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Az Azure szerepköralapú hozzáférés-vezérlés használata Azure Backup helyreállítási pontok kezeléséhez

Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) lehetővé teszi a részletes hozzáférés-kezelést az Azure-hoz. Az Azure RBAC használata lehetővé teszi, hogy elkülönítse a kötelességeket a csapaton belül, valamint csak olyan mértékű hozzáférést biztosítson, amelyre a felhasználóknak a feladataik elvégzéséhez szükségük van.

> [!IMPORTANT]
> Az Azure Backup által biztosított szerepkörök csak olyan műveletekre korlátozódnak, amelyek Azure Portal vagy REST API vagy Recovery Services Vault PowerShell vagy CLI parancsmagok segítségével hajthatók végre. A Azure Backup ügynök ügyfél felhasználói felületén vagy a System Center Data Protection Manager felhasználói felületen vagy Azure Backup Server felhasználói felületen végrehajtott műveletek nem tartoznak a szerepkörök felügyeletéhez.

A Azure Backup három beépített szerepkört biztosít a biztonságimásolat-kezelési műveletek vezérléséhez. További információ az [Azure beépített szerepköreiről](../role-based-access-control/built-in-roles.md)

* [Biztonsági másolat közreműködői](../role-based-access-control/built-in-roles.md#backup-contributor) – ez a szerepkör rendelkezik a biztonsági másolatok létrehozásához és kezeléséhez szükséges engedélyekkel, kivéve a Recovery Services-tároló törlését és másokhoz való hozzáférést biztosít. Képzelje el ezt a szerepkört a biztonsági mentési felügyelet rendszergazdájának, aki minden biztonsági mentési felügyeleti műveletet végrehajthat.
* [Biztonságimásolat-felelős](../role-based-access-control/built-in-roles.md#backup-operator) – ez a szerepkör a biztonsági mentést és a biztonsági mentési házirendek felügyeletét kivéve minden közreműködőhöz rendelkezik engedéllyel. Ez a szerepkör egyenértékű a közreműködővel, kivéve, ha nem tud olyan romboló műveleteket végezni, mint például a biztonsági mentés leállítása vagy a helyszíni erőforrások regisztrációjának törlése.
* [Backup Reader](../role-based-access-control/built-in-roles.md#backup-reader) – ez a szerepkör jogosult az összes biztonsági mentési felügyeleti művelet megtekintésére. Képzelje el, hogy ezt a szerepkört figyelő személynek kell lennie.

Ha a saját szerepköröket is meg szeretné határozni még több szabályozáshoz, tekintse meg az [Egyéni szerepkörök létrehozása](../role-based-access-control/custom-roles.md) az Azure RBAC című témakört.

## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Beépített szerepkörök biztonsági mentésének leképezése biztonsági mentési felügyeleti műveletekre

### <a name="minimum-role-requirements-for-azure-vm-backup"></a>Az Azure virtuális gépek biztonsági mentésének minimális szerepköri követelményei

Az alábbi táblázat a művelet végrehajtásához szükséges biztonságimásolat-kezelési műveleteket és a hozzá tartozó minimális Azure-szerepkört rögzíti.

| Kezelési művelet | Minimálisan szükséges Azure-szerepkör | Hatókör szükséges | Alternatív |
| --- | --- | --- | --- |
| Helyreállítási tár létrehozása | Biztonsági mentési közreműködő | A tárolót tartalmazó erőforráscsoport |   |
| Azure-beli virtuális gépek biztonsági mentésének engedélyezése | Biztonságimásolat-felelős | A tárolót tartalmazó erőforráscsoport |   |
| | Virtuális gépek közreműködője | VM-erőforrás |  A beépített szerepkör helyett a következő engedélyekkel rendelkező egyéni szerepkört is megtekintheti: Microsoft. számítás/virtualMachines/írás |
| Virtuális gép igény szerinti biztonsági mentése | Biztonságimásolat-felelős | Recovery Services-tároló |   |
| Virtuális gép visszaállítása | Biztonságimásolat-felelős | Recovery Services-tároló |   |
| | Közreműködő | Az erőforráscsoport, amelyben a virtuális gép üzembe lesz helyezve |   A beépített szerepkör helyett olyan egyéni szerepkört is megadhat, amely a következő engedélyekkel rendelkezik: Microsoft. Resources/Subscriptions/resourceGroups/Write Microsoft. DomainRegistration/Domains/Write, Microsoft. számítás/virtualMachines/Write Microsoft. Network/virtualNetworks/Read Microsoft. Network/virtualNetworks/alhálózatok/csatlakozás/művelet | 
| | Virtuális gépek közreműködője | A forrásként szolgáló virtuális gép, amelyről biztonsági mentés készül |   A beépített szerepkör helyett a következő engedélyekkel rendelkező egyéni szerepkört is megtekintheti: Microsoft. számítás/virtualMachines/írás |
| Nem felügyelt lemezek visszaállítása virtuális gép biztonsági mentése | Biztonságimásolat-felelős | Recovery Services-tároló |
| | Virtuális gépek közreműködője | A forrásként szolgáló virtuális gép, amelyről biztonsági mentés készül | A beépített szerepkör helyett a következő engedélyekkel rendelkező egyéni szerepkört is megtekintheti: Microsoft. számítás/virtualMachines/írás |
| | Tárfiók-közreműködő | A Storage-fiók erőforrása, ahol a lemezek vissza lesznek állítva |   A beépített szerepkör helyett a következő engedélyekkel rendelkező egyéni szerepkört is megtekintheti: Microsoft. Storage/storageAccounts/Write |
| Felügyelt lemezek visszaállítása a virtuális gép biztonsági másolatából | Biztonságimásolat-felelős | Recovery Services-tároló |
| | Virtuális gépek közreműködője | A forrásként szolgáló virtuális gép, amelyről biztonsági mentés készül |    A beépített szerepkör helyett a következő engedélyekkel rendelkező egyéni szerepkört is megtekintheti: Microsoft. számítás/virtualMachines/írás |
| | Tárfiók-közreműködő | A visszaállítás részeként kijelölt ideiglenes Storage-fiók, amely a tárolóból származó adatok tárolására szolgál, mielőtt átalakítja őket a felügyelt lemezekre. |   A beépített szerepkör helyett a következő engedélyekkel rendelkező egyéni szerepkört is megtekintheti: Microsoft. Storage/storageAccounts/Write |
| | Közreműködő | Az erőforráscsoport, amelybe a felügyelt lemez (ek) vissza lesz állítva | A beépített szerepkör helyett a következő engedélyekkel rendelkező egyéni szerepkört is megtekintheti: Microsoft. Resources/Subscriptions/resourceGroups/Write|
| Egyedi fájlok visszaállítása a virtuális gép biztonsági másolatából | Biztonságimásolat-felelős | Recovery Services-tároló |
| | Virtuális gépek közreműködője | A forrásként szolgáló virtuális gép, amelyről biztonsági mentés készül | A beépített szerepkör helyett a következő engedélyekkel rendelkező egyéni szerepkört is megtekintheti: Microsoft. számítás/virtualMachines/írás |
| Biztonsági mentési szabályzat létrehozása az Azure-beli virtuális gépek biztonsági mentéséhez | Biztonsági mentési közreműködő | Recovery Services-tároló |
| Azure-beli virtuális gép biztonsági mentési szabályzatának módosítása | Biztonsági mentési közreműködő | Recovery Services-tároló |
| Azure-beli virtuális gép biztonsági mentési szabályzatának törlése | Biztonsági mentési közreműködő | Recovery Services-tároló |
| Biztonsági mentés leállítása (az adat megőrzése vagy az adat törlése) a virtuális gép biztonsági mentésében | Biztonsági mentési közreműködő | Recovery Services-tároló |
| Helyszíni Windows Server/Client/SCDPM vagy Azure Backup Server regisztrálása | Biztonságimásolat-felelős | Recovery Services-tároló |
| Regisztrált helyszíni Windows Server/Client/SCDPM vagy Azure Backup Server törlése | Biztonsági mentési közreműködő | Recovery Services-tároló |

> [!IMPORTANT]
> Ha virtuálisgép-erőforrás-hatókörben adja meg a virtuálisgép-közreműködőt, és a virtuálisgép-beállítások részeként kiválasztja a **biztonsági mentést** , megnyílik a **biztonsági mentés engedélyezése** képernyő, még akkor is, ha a virtuális gép már biztonsági másolattal rendelkezik. Ennek az az oka, hogy a biztonsági mentési állapot ellenőrzésének meghívása csak az előfizetési szinten működik. Ennek elkerüléséhez nyissa meg a tárolót, és nyissa meg a virtuális gép biztonsági mentési elemének nézetét, vagy a virtuálisgép-közreműködő szerepkört egy előfizetési szinten kell megadnia.

### <a name="minimum-role-requirements-for-azure-workload-backups-sql-and-hana-db-backups"></a>Az Azure munkaterhelés biztonsági mentéséhez szükséges minimális szerepkör-követelmények (SQL-és HANA-adatbázis biztonsági mentései)

Az alábbi táblázat a művelet végrehajtásához szükséges biztonságimásolat-kezelési műveleteket és a hozzá tartozó minimális Azure-szerepkört rögzíti.

| Kezelési művelet | Minimálisan szükséges Azure-szerepkör | Hatókör szükséges | Alternatív |
| --- | --- | --- | --- |
| Helyreállítási tár létrehozása | Biztonsági mentési közreműködő | A tárolót tartalmazó erőforráscsoport |   |
| SQL-és/vagy HANA-adatbázisok biztonsági mentésének engedélyezése | Biztonságimásolat-felelős | A tárolót tartalmazó erőforráscsoport |   |
| | Virtuális gépek közreműködője | VM-erőforrás, amelyben az adatbázis telepítve van |  A beépített szerepkör helyett a következő engedélyekkel rendelkező egyéni szerepkört is megtekintheti: Microsoft. számítás/virtualMachines/írás |
| Az adatbázis igény szerinti biztonsági mentése | Biztonságimásolat-felelős | Recovery Services-tároló |   |
| Adatbázis visszaállítása vagy visszaállítás fájlként | Biztonságimásolat-felelős | Recovery Services-tároló |   |
| | Virtuális gépek közreműködője | A forrásként szolgáló virtuális gép, amelyről biztonsági mentés készül |   A beépített szerepkör helyett a következő engedélyekkel rendelkező egyéni szerepkört is megtekintheti: Microsoft. számítás/virtualMachines/írás |
| | Virtuális gépek közreműködője | Cél virtuális gép, amelybe a rendszer visszaállítja vagy létrehozta a fájlokat |   A beépített szerepkör helyett a következő engedélyekkel rendelkező egyéni szerepkört is megtekintheti: Microsoft. számítás/virtualMachines/írás |
| Biztonsági mentési szabályzat létrehozása az Azure-beli virtuális gépek biztonsági mentéséhez | Biztonsági mentési közreműködő | Recovery Services-tároló |
| Azure-beli virtuális gép biztonsági mentési szabályzatának módosítása | Biztonsági mentési közreműködő | Recovery Services-tároló |
| Azure-beli virtuális gép biztonsági mentési szabályzatának törlése | Biztonsági mentési közreműködő | Recovery Services-tároló |
| Biztonsági mentés leállítása (az adat megőrzése vagy az adat törlése) a virtuális gép biztonsági mentésében | Biztonsági mentési közreműködő | Recovery Services-tároló |

### <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Az Azure-fájlmegosztás biztonsági mentésének minimális szerepköri követelményei

Az alábbi táblázat rögzíti az Azure fájlmegosztás művelet végrehajtásához szükséges biztonságimásolat-kezelési műveleteket és a hozzá tartozó szerepkört.

| Kezelési művelet | Szerepkör szükséges | További források |
| --- | --- | --- |
| Azure-fájlmegosztás biztonsági mentésének engedélyezése | Biztonsági mentési közreműködő |Recovery Services-tároló |
| |Tárfiók | Közreműködői Storage-fiók erőforrása |
| Virtuális gép igény szerinti biztonsági mentése | Biztonságimásolat-felelős | Recovery Services-tároló |
| Fájlmegosztás visszaállítása | Biztonságimásolat-felelős | Recovery Services-tároló |
| | Tárfiók-közreműködő | A Storage-fiók erőforrásai, ahol a visszaállítási forrás és a célfájl-megosztások találhatók |
| Egyedi fájlok visszaállítása | Biztonságimásolat-felelős | Recovery Services-tároló |
| |Tárfiók-közreműködő|A Storage-fiók erőforrásai, ahol a visszaállítási forrás és a célfájl-megosztások találhatók |
| A védelem kikapcsolása |Biztonsági mentési közreműködő | Recovery Services-tároló |
| Storage-fiók regisztrációjának törlése a tárból |Biztonsági mentési közreműködő | Recovery Services-tároló |
| |Tárfiók-közreműködő | Storage-fiók erőforrása|

## <a name="next-steps"></a>Következő lépések

* [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/role-assignments-portal.md): Ismerkedés az Azure RBAC a Azure Portal.
* Ismerje meg, hogyan kezelheti a hozzáférést a következővel:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* Az [Azure szerepköralapú hozzáférés-vezérlésével kapcsolatos hibaelhárítás](../role-based-access-control/troubleshooting.md): javaslatokat kaphat a gyakori problémák megoldására.
