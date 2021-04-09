---
title: Biztonsági mentés és visszaállítás Active Directory
description: Megtudhatja, hogyan készíthet biztonsági mentést és visszaállítást Active Directory tartományvezérlőkön.
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 8db2dab605e90e4748b11a632d6651c23d631b6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98733553"
---
# <a name="back-up-and-restore-active-directory-domain-controllers"></a>Active Directory tartományvezérlők biztonsági mentése és visszaállítása

A Active Directory biztonsági mentése, valamint a sikeres visszaállítások biztosítása a korrupció, a kompromisszum vagy a katasztrófa kritikus része a Active Directory karbantartásának.

Ez a cikk ismerteti a Active Directory tartományvezérlők biztonsági mentésének és visszaállításának megfelelő eljárásait Azure Backup, akár Azure-beli virtuális gépeken, akár helyszíni kiszolgálókon. Egy olyan forgatókönyvet tárgyal, amelyben egy teljes tartományvezérlőt kell visszaállítani a biztonsági mentés időpontjában. Ha szeretné megtekinteni, hogy melyik visszaállítási forgatókönyv megfelelő, tekintse meg [ezt a cikket](/windows-server/identity/ad-ds/manage/ad-forest-recovery-determine-how-to-recover).  

>[!NOTE]
> Ez a cikk nem tárgyalja a [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)elemeinek visszaállítását. Azure Active Directory felhasználók visszaállításával kapcsolatos információkért tekintse meg [ezt a cikket](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="best-practices"></a>Ajánlott eljárások

- Győződjön meg arról, hogy legalább egy tartományvezérlőről készül biztonsági másolat. Ha több tartományvezérlőről készít biztonsági mentést, győződjön meg arról, hogy az FSMO-t [(a rugalmas, egyetlen főkiszolgálói művelet) tartalmazó szerepköröket](/windows-server/identity/ad-ds/plan/planning-operations-master-role-placement) tároló összes felhasználó biztonsági mentést készít.

- Active Directory gyakori biztonsági mentése. A biztonsági mentés soha nem lehet nagyobb, mint a törlésre kijelöltség élettartama (alapértelmezés szerint 60 nap), mert a törlésre kijelölt időpontnál régebbi objektumok "törlésre kerülnek", és a továbbiakban nem tekintendők érvényesnek.

- Törölje a vész-helyreállítási tervet, amely a tartományvezérlők visszaállítására vonatkozó utasításokat tartalmaz. Active Directory-erdő helyreállításának előkészítéséhez olvassa el az [Active Directory erdő helyreállítási útmutatóját](/windows-server/identity/ad-ds/manage/ad-forest-recovery-guide).

- Ha egy tartományvezérlőt vissza kell állítania, és rendelkeznie kell egy további működő tartományvezérlővel a tartományban, a biztonsági másolatból való visszaállítás helyett új kiszolgálót is készíthet. Adja hozzá a **Active Directory Domain Services** kiszolgálói szerepkört az új kiszolgálóhoz, hogy a tartományvezérlő legyen a meglévő tartományban. Ezután a rendszer az új kiszolgálóra replikálja a Active Directory-adatforrást. Az előző tartományvezérlő Active Directoryból való eltávolításához kövesse a [cikk](/windows-server/identity/ad-ds/deploy/ad-ds-metadata-cleanup) lépéseit a metaadatok karbantartásának elvégzéséhez.

>[!NOTE]
>A Azure Backup nem tartalmazza a Active Directory elemszintű visszaállítását. Ha vissza szeretné állítani a törölt objektumokat, és egy tartományvezérlőhöz fér hozzá, használja a [Active Directory Lomtárát](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#ad_recycle_bin_mgmt). Ha ez a metódus nem érhető el, a tartományvezérlő biztonsági másolatával visszaállíthatja a törölt objektumokat a **ntdsutil.exe** eszközzel az [itt](https://support.microsoft.com/help/840001/how-to-restore-deleted-user-accounts-and-their-group-memberships-in-ac)leírtak szerint.
>
>A SYSVOL mappa mérvadó visszaállításának végrehajtásával kapcsolatos információkért tekintse meg [ezt a cikket](/windows-server/identity/ad-ds/manage/ad-forest-recovery-authoritative-recovery-sysvol).

## <a name="backing-up-azure-vm-domain-controllers"></a>Azure-beli VM-tartományvezérlők biztonsági mentése

Ha a tartományvezérlő egy Azure-beli virtuális gép, biztonsági mentést készíthet a kiszolgálóról az [Azure VM Backup](backup-azure-vms-introduction.md)használatával.

További információ a [virtualizált tartományvezérlők működési szempontjairól:](/windows-server/identity/ad-ds/get-started/virtual-dc/virtualized-domain-controllers-hyper-v#operational-considerations-for-virtualized-domain-controllers) az Azure-beli virtuálisgép-tartományvezérlők sikeres biztonsági mentése (és későbbi visszaállítása).

## <a name="backing-up-on-premises-domain-controllers"></a>Helyszíni tartományvezérlők biztonsági mentése

Helyszíni tartományvezérlő biztonsági mentéséhez biztonsági mentést kell készítenie a kiszolgáló rendszerállapot-információi közül.

- Ha a MARSot használja, kövesse [az alábbi utasításokat](backup-azure-system-state.md).
- Ha a MABS (Azure Backup Server) használja, kövesse [az alábbi utasításokat](backup-mabs-system-state-and-bmr.md).

>[!NOTE]
> A helyszíni tartományvezérlők (akár rendszerállapotból, akár virtuális gépekről) az Azure-felhőbe való visszaállítása nem támogatott. Ha a helyszíni Active Directory-környezetből az Azure-ba szeretne feladatátvételt használni, érdemes lehet [Azure site Recovery](../site-recovery/site-recovery-active-directory.md).

## <a name="restoring-active-directory"></a>Active Directory visszaállítása

Active Directory az adatmennyiséget két mód egyikében lehet visszaállítani: **mérvadó** vagy nem **mérvadó**. A mérvadó visszaállítás során a visszaállított Active Directoryi adatértékek felülbírálják az erdő többi tartományvezérlőjén található adatforrásokat.

Ebben a forgatókönyvben azonban egy meglévő tartományban lévő tartományvezérlőt építünk át, ezért nem **mérvadó** visszaállítást kell végrehajtani.

A visszaállítás során a kiszolgáló a Címtárszolgáltatások helyreállító módjában lesz elindítva. A Címtárszolgáltatások helyreállító módjához meg kell adnia a rendszergazdai jelszót.

>[!NOTE]
>Ha elfelejti a Címtárszolgáltatások helyreállító módjának jelszavát, [ezeket az utasításokat követve](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc754363(v=ws.11))állíthatja vissza.

### <a name="restoring-azure-vm-domain-controllers"></a>Azure-beli virtuális gépek tartományvezérlőinek visszaállítása

Az Azure-beli virtuálisgép-tartományvezérlők visszaállításával kapcsolatban lásd: [tartományvezérlő virtuális gépek visszaállítása](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).

Ha egyetlen tartományvezérlői virtuális gépet vagy több tartományvezérlő virtuális gépet állít vissza egyetlen tartományba, állítsa vissza, mint bármely más virtuális gépet. A Címtárszolgáltatások helyreállító módja (címtárszolgáltatás-javító mód) is elérhető, így az összes Active Directory-helyreállítási forgatókönyv életképes.

Ha egy tartományvezérlő virtuális gépet egy több tartományi konfigurációban kell visszaállítani, állítsa vissza a lemezeket, és hozzon létre egy virtuális gépet a [PowerShell használatával](backup-azure-vms-automation.md#restore-the-disks).

Ha a tartomány utolsó fennmaradó tartományvezérlőjét állítja vissza, vagy egy erdő több tartományát állítja vissza, javasoljuk, hogy az [erdő helyreállítását](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).

>[!NOTE]
> A virtualizált tartományvezérlők a Windows 2012-től kezdve [virtualizációs alapú védelmet](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100#virtualization-based-safeguards)használnak. Ezekkel a garanciákkal az Active Directory tudomásul veszi, hogy a helyreállított virtuális gép tartományvezérlő-e, és elvégzi a Active Directory adatok visszaállításához szükséges lépéseket.

### <a name="restoring-on-premises-domain-controllers"></a>Helyszíni tartományvezérlők visszaállítása

A helyszíni tartományvezérlők visszaállításához kövesse a rendszerállapot visszaállítása a Windows Server rendszerre című témakör útmutatását a [rendszerállapot-helyreállításra vonatkozó speciális szempontokkal kapcsolatban a tartományvezérlőn](backup-azure-restore-system-state.md#special-considerations-for-system-state-recovery-on-a-domain-controller).

## <a name="next-steps"></a>Következő lépések

- [Azure Backup támogatási mátrixa](backup-support-matrix.md)