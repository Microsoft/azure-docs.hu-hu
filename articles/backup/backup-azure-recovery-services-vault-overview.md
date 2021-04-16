---
title: A Recovery Services-tárolók áttekintése
description: A Recovery Services-tárolók áttekintése.
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: 2f2018f0f3d3135d632418c2e591e6ad938d62d2
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517453"
---
# <a name="recovery-services-vaults-overview"></a>A helyreállítási tárak áttekintése

Ez a cikk a Recovery Services-tárolók funkcióit ismerteti. A Recovery Services-tároló egy adatokat tároló tároló entitás az Azure-ban. Az adatok általában az adatok másolatai, vagy a virtuális gépek( virtuális gépek), számítási feladatok, kiszolgálók vagy munkaállomások konfigurációs információi. A Recovery Services-tárolók segítségével különböző Azure-szolgáltatások, például IaaS virtuális gépek (Linux vagy Windows) biztonsági mentési adatait és Azure SQL is. A Recovery Services-tárolók támogatják System Center DPM, Windows Server, Azure Backup Server és egyéb szolgáltatásokat. A Recovery Services-tárolók leegyszerűsítik a biztonsági mentési adatok szervezését, miközben minimálisra csökkentik a munkaterhelést. A Recovery Services-tárolók az Azure Azure Resource Manager modellen alapulnak, amely a következő funkciókat biztosítja:

- **Továbbfejlesztett képességek az** adatok biztonsági mentésének védelméhez: A Azure Backup-tárolók biztonsági képességeket biztosítanak a felhőalapú biztonsági mentések védelméhez. A biztonsági funkciók biztosítják a biztonsági másolatok biztonságát és az adatok biztonságos helyreállítását még akkor is, ha az éles és a biztonsági mentési kiszolgálók biztonsága sérül. [További információ](backup-azure-security-feature.md)

- **A hibrid it-környezet** központi monitorozása: A Recovery [Services-tárolók](backup-azure-manage-windows-server.md#manage-backup-items) használatával nem csak az [Azure IaaS](backup-azure-manage-vms.md) virtuális gépeket, hanem a helyszíni eszközöket is monitorozhatja egy központi portálról. [További információ](backup-azure-monitoring-built-in-monitor.md)

- **Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC):** Az Azure RBAC részletes hozzáférés-vezérlést biztosít az Azure-ban. [Az Azure számos beépített szerepkört](../role-based-access-control/built-in-roles.md)biztosít, Azure Backup három beépített szerepköre van a [helyreállítási pontok kezeléséhez.](backup-rbac-rs-vault.md) A Recovery Services-tárolók kompatibilisek az Azure RBAC-val, amely a biztonsági mentést és a visszaállítást a felhasználói szerepkörök meghatározott halmazára korlátozza. [További információ](backup-rbac-rs-vault.md)

- **Helyreállító** törlés: A helyreállított törlés még akkor is lehetővé teszi a biztonsági mentési elem helyreállítását adatvesztés nélkül, ha egy rosszindulatú felhasználó töröl egy biztonsági másolatot (vagy véletlenül törli a biztonsági mentési adatokat), a rendszer további 14 napig megőrzi a biztonsági mentési adatokat. A "soft delete" állapotban a biztonsági másolatok adatainak további 14 napos megőrzési időszaka nem jár költséggel. [További információ](backup-azure-security-feature-cloud.md).

- **Régiók közötti visszaállítás:** A régiók közötti visszaállítás (CRR) lehetővé teszi az Azure-beli virtuális gépek visszaállítását egy másodlagos régióban, amely egy párosított Azure-régió. Ha engedélyezi ezt a funkciót a tároló [szintjén,](backup-create-rs-vault.md#set-cross-region-restore)bármikor visszaállíthatja a replikált adatokat a másodlagos régióban, ha úgy dönt. Ez lehetővé teszi a másodlagos régió adatainak visszaállítását a naplózási megfelelőséghez és a szolgáltatáskimaradási forgatókönyvekhez anélkül, hogy meg kellene várnia, hogy az Azure katasztrófa bejelentsen (ellentétben a tároló GRS-beállításaival). [További információ](backup-azure-arm-restore-vms.md#cross-region-restore).

## <a name="storage-settings-in-the-recovery-services-vault"></a>Tárolási beállítások a Recovery Services-tárolóban

A Recovery Services-tároló egy olyan entitás, amely az idő során létrehozott biztonsági másolatokat és helyreállítási pontokat tárolja. A Recovery Services-tároló a védett virtuális gépekhez társított biztonsági mentési szabályzatokat is tartalmazza.

- Azure Backup automatikusan kezeli a tároló tárterületét. Tekintse meg, [hogyan módosíthatók a tárolási beállítások.](./backup-create-rs-vault.md#set-storage-redundancy)

- A tárhely-redundanciával kapcsolatos további információkért [](../storage/common/storage-redundancy.md#locally-redundant-storage) tekintse meg a [geo,](../storage/common/storage-redundancy.md#geo-zone-redundant-storage)a helyi és a zóna redundanciával [kapcsolatos cikkeket.](../storage/common/storage-redundancy.md#zone-redundant-storage)

## <a name="encryption-settings-in-the-recovery-services-vault"></a>Titkosítási beállítások a Recovery Services-tárolóban

Ez a szakasz a Recovery Services-tárolóban tárolt biztonsági mentési adatok titkosításához rendelkezésre álló lehetőségeket ismerteti.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Biztonsági mentési adatok titkosítása platform által kezelt kulcsokkal

Alapértelmezés szerint minden adata platform által kezelt kulcsokkal van titkosítva. A titkosítás engedélyezéséhez semmilyen explicit műveletet nem kell tennie a végpontról. A helyreállítási tárba biztonságimentett összes számítási feladatra vonatkozik.

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Biztonsági mentési adatok titkosítása ügyfelek által felügyelt kulcsok használatával

Dönthet úgy, hogy az adatokat az Ön tulajdonában lévő és kezelt titkosítási kulcsokkal titkosítja. Azure Backup lehetővé teszi, hogy a biztonsági másolatok titkosításához használja a Azure Key Vault tárolóban tárolt RSA-kulcsokat. A biztonsági másolatok titkosításához használt titkosítási kulcs különbözhet a forráshoz használt kulcstól. Az adatok védelme AES 256-alapú adattitkosítási kulccsal (DEK), amely a kulcsokkal védett. Így teljes körűen szabályozhatja az adatokat és a kulcsokat. A titkosításhoz a Recovery Services-tárolónak hozzáférést kell biztosítani a titkosítási kulcshoz a Azure Key Vault. Letilthatja a kulcsot, vagy szükség esetén visszavonhatja a hozzáférést. A kulcsokat használó titkosítást azonban engedélyeznie kell, mielőtt megkíséreli megvédeni a tároló elemeit.

További információ a biztonsági másolatok adatainak ügyfél által kezelt kulcsokkal [való titkosításával kapcsolatban.](encryption-at-rest-with-cmk.md)

## <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](../advisor/index.yml) egy személyre szabott felhőtanácsadó, amely segít optimalizálni az Azure használatát. Elemzi az Azure-használatot, és időben javaslatokat tesz az üzemelő példányok optimalizálásához és biztonságossá helyezéséhez. Négy kategóriában kínál javaslatokat: magas rendelkezésre állás, biztonság, teljesítmény és költség.

Azure Advisor óránkénti javaslatokat [](../advisor/advisor-high-availability-recommendations.md#protect-your-virtual-machine-data-from-accidental-deletion) tesz olyan virtuális gépekhez, amelyekről nem jön biztonsági jelentés, így soha ne hagyja ki a fontos virtuális gépek biztonságimentését. A javaslatokat úgy is szabályozhatja, ha eltolást ad.  A javaslat kiválasztásával és a virtuális gépeken való biztonsági mentés engedélyezéséhez megadhatja a tárolót (a biztonsági másolatok tárolására) és a biztonsági mentési szabályzatot (a biztonsági másolatok ütemezését és a biztonsági másolatok megőrzését).

![Azure Advisor](./media/backup-azure-recovery-services-vault-overview/azure-advisor.png)

## <a name="additional-resources"></a>További források

- [A tároló támogatott és nem támogatott forgatókönyvei](backup-support-matrix.md#vault-support)
- [A tárolóval kapcsolatos gyakori kérdések](backup-azure-backup-faq.yml)

## <a name="next-steps"></a>Következő lépések

A következő cikkek a következőkre használhatók:

- [IaaS virtuális gép biztonsági visszaállítása](backup-azure-arm-vms-prepare.md)
- [Biztonsági Azure Backup Server](backup-azure-microsoft-azure-backup.md)
- [Windows Server biztonsági telepítése](backup-windows-with-mars-agent.md)
