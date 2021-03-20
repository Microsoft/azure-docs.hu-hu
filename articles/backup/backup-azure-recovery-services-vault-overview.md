---
title: A Recovery Services-tárolók áttekintése
description: Recovery Services-tárolók áttekintése.
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: 0ed37446e1ccf0780f924143c8f063964adf0004
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98755113"
---
# <a name="recovery-services-vaults-overview"></a>A helyreállítási tárak áttekintése

Ez a cikk egy Recovery Services-tároló funkcióit ismerteti. A Recovery Services-tároló egy Azure-beli tárolási entitás, amely az adattárolást. Az adatok általában adatok másolatai, vagy a virtuális gépek (VM), a munkaterhelések, a kiszolgálók és a munkaállomások konfigurációs adatai. Recovery Services-tárolók használatával a különböző Azure-szolgáltatások, például a IaaS-alapú virtuális gépek (Linux vagy Windows) és az Azure SQL Database-adatbázisok biztonsági másolatait is megtarthatja. Recovery Services-tárolók támogatják a System Center DPM, a Windows Servert, a Azure Backup Server és egyebeket. A Recovery Services-tárolók leegyszerűsítik a biztonsági mentési adatok szervezését, miközben minimálisra csökkentik a munkaterhelést. Recovery Services-tárolók az Azure Azure Resource Manager modelljén alapulnak, amely a következő funkciókat kínálja:

- **Továbbfejlesztett funkciók a biztonsági mentési adatokat biztonságossá tételéhez**: a Recovery Services-tárolók révén a Azure Backup biztonsági funkciókat biztosít a Felhőbeli biztonsági másolatok védelméhez. A biztonsági funkciók biztosítják, hogy biztonságban legyenek a biztonsági másolatok, és biztonságosan helyreállítsák az adatokat, még akkor is, ha a termelési és a biztonsági mentési kiszolgálók biztonsága sérül. [További információ](backup-azure-security-feature.md)

- **A hibrid informatikai környezet központi monitorozása**: Recovery Services-tárolókkal nem csak az [Azure IaaS virtuális gépeket](backup-azure-manage-vms.md) , hanem a központi portálon található helyszíni [eszközöket](backup-azure-manage-windows-server.md#manage-backup-items) is figyelheti. [További információ](backup-azure-monitoring-built-in-monitor.md)

- **Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)**: az Azure RBAC részletes hozzáférés-vezérlési felügyeletet biztosít az Azure-ban. Az [Azure számos beépített szerepkört kínál](../role-based-access-control/built-in-roles.md), és a Azure Backup három [beépített szerepkörrel rendelkezik a helyreállítási pontok kezeléséhez](backup-rbac-rs-vault.md). Recovery Services-tárolók kompatibilisek az Azure RBAC, amely korlátozza a biztonsági mentést és a hozzáférés visszaállítását a meghatározott felhasználói szerepkörökhöz. [További információ](backup-rbac-rs-vault.md)

- Helyreállítható **Törlés**: a helyreállítható törléssel még akkor is, ha rosszindulatú színész törli a biztonsági másolatokat (vagy véletlenül törli a biztonsági mentési adatmennyiséget), a biztonsági mentési adat 14 további napig tart, így a biztonsági mentési elem helyreállítása adatvesztés nélkül történik. A "Soft Delete" állapotban lévő biztonsági mentési adatok megőrzésének további 14 napja nem jár semmilyen költséggel. [További információ](backup-azure-security-feature-cloud.md).

- **Régiók közötti visszaállítás**: a régiók közötti visszaállítás (CRR) lehetővé teszi az Azure-beli virtuális gépek visszaállítását egy másodlagos régióban, amely egy Azure párosított régió. Ha engedélyezi ezt a szolgáltatást a tároló [szintjén](backup-create-rs-vault.md#set-cross-region-restore), bármikor visszaállíthatja a replikált adatterületet a másodlagos régióban, ha a lehetőséget választja. Ez lehetővé teszi, hogy visszaállítsa a másodlagos régióhoz tartozó adatok naplózási megfelelőségét, és a leállás ideje alatt, anélkül, hogy az Azure-nak kellene megvárnia a katasztrófát (a tár GRS beállításaitól eltérően). [További információ](backup-azure-arm-restore-vms.md#cross-region-restore).

## <a name="storage-settings-in-the-recovery-services-vault"></a>Tárolási beállítások a Recovery Services-tárolóban

A Recovery Services-tároló egy olyan entitás, amely az idő múlásával létrehozott biztonsági mentéseket és helyreállítási pontokat tárolja. A Recovery Services-tároló a védett virtuális gépekhez társított biztonsági mentési házirendeket is tartalmazza.

- A Azure Backup automatikusan kezeli a tároló tárterületét. Megtudhatja, hogyan [módosíthatók a tárolási beállítások](./backup-create-rs-vault.md#set-storage-redundancy).

- A tárterület-redundanciával kapcsolatos további tudnivalókért tekintse meg a [geo](../storage/common/storage-redundancy.md#geo-zone-redundant-storage), a [helyi](../storage/common/storage-redundancy.md#locally-redundant-storage) és a [zonay](../storage/common/storage-redundancy.md#zone-redundant-storage) redundancia című cikket.

## <a name="encryption-settings-in-the-recovery-services-vault"></a>Titkosítási beállítások a Recovery Services-tárolóban

Ez a szakasz a Recovery Services-tárolóban tárolt biztonsági másolatok titkosításához rendelkezésre álló lehetőségeket ismerteti.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Biztonsági másolatok titkosítása a platform által felügyelt kulcsokkal

Alapértelmezés szerint az összes adatait a platform által felügyelt kulcsok használatával titkosítja a rendszer. A titkosítás engedélyezéséhez nem szükséges explicit műveletet végrehajtani a végponttól. Ez a Recovery Services-tárolóba mentett összes munkaterhelésre vonatkozik.

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Biztonsági mentési adatok titkosítása ügyfelek által felügyelt kulcsok használatával

Dönthet úgy, hogy az Ön által birtokolt és felügyelt titkosítási kulcsok használatával titkosítja az adatait. Azure Backup lehetővé teszi, hogy a Azure Key Vault tárolt RSA-kulcsait a biztonsági mentések titkosításához használja. A biztonsági mentések titkosításához használt titkosítási kulcs különbözhet a forráshoz használttól. Az adatait egy AES 256-alapú adattitkosítási kulccsal (ADATTITKOSÍTÁSI kulcsot) védik, amely viszont a kulcsok használatával védett. Ez teljes körű irányítást biztosít az adat és a kulcsok felett. A titkosítás engedélyezéséhez a Recovery Services-tárolónak hozzáférést kell biztosítania a Azure Key Vault található titkosítási kulcshoz. Ha szükséges, letilthatja a kulcsot, vagy visszavonhatja a hozzáférést. A titkosítást azonban a kulcsok használatával kell engedélyeznie, mielőtt bármilyen elemet a tárolóhoz próbál védelemmel ellátni.

További információ arról, hogyan titkosíthatja a biztonsági mentési adatait az [ügyfél által felügyelt kulcsokkal](encryption-at-rest-with-cmk.md).

## <a name="azure-advisor"></a>Azure Advisor

A [Azure Advisor](../advisor/index.yml) egy személyre szabott felhőalapú tanácsadó, amely segít optimalizálni az Azure használatát. Elemzi az Azure-használatot, és időben ajánlásokat nyújt az üzemelő példányok optimalizálásához és biztonságossá tételéhez. Négy kategóriában nyújt javaslatokat: magas rendelkezésre állás, biztonság, teljesítmény és költséghatékonyság.

A Azure Advisor óránként [javaslatokat](../advisor/advisor-high-availability-recommendations.md#protect-your-virtual-machine-data-from-accidental-deletion) nyújt a virtuális gépekről, amelyekről nem készül biztonsági mentés, így soha nem marad a fontos virtuális gépek biztonsági mentése. Az ajánlásokat úgy is szabályozhatja, hogy a késleltetéssel megadhatja őket.  Kiválaszthatja a javaslatot, és engedélyezheti a biztonsági mentést a virtuális gépeken. Ehhez adja meg a tárolót (ahol a biztonsági másolatok lesznek tárolva) és a biztonsági mentési szabályzatot (a biztonsági másolatok ütemezése és a biztonsági másolatok megőrzése).

![Azure Advisor](./media/backup-azure-recovery-services-vault-overview/azure-advisor.png)

## <a name="additional-resources"></a>További források

- [Tár támogatott és nem támogatott forgatókönyvek](backup-support-matrix.md#vault-support)
- [Tár – gyakori kérdések](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Következő lépések

A következő cikkek használhatók:

- [IaaS virtuális gép biztonsági mentése](backup-azure-arm-vms-prepare.md)
- [Azure Backup Server biztonsági mentése](backup-azure-microsoft-azure-backup.md)
- [Windows Server biztonsági mentése](backup-windows-with-mars-agent.md)
