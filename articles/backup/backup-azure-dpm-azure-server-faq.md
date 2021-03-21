---
title: Azure Backup Server és DPM – gyakori kérdések
description: Ebben a cikkben a Microsoft Azure Backup-kiszolgálóval (MABS) és a DPM (Data Protection Manager) kapcsolatos gyakori kérdésekre adott válaszokat talál.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/05/2019
ms.openlocfilehash: 1663a842b7e00c611543451d4caef96b5b5a913f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97954991"
---
# <a name="azure-backup-server-and-dpm---faq"></a>Azure Backup Server és DPM – gyakori kérdések

## <a name="general-questions"></a>Általános kérdések

Ez a cikk a Azure Backup Server és a DPM kapcsolatos gyakori kérdésekre ad választ.

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server"></a>Használhatom az Azure Backup kiszolgálót egy operációs rendszer nélküli helyreállítás (BMR) biztonsági másolatának létrehozásához egy fizikai kiszolgálón?

Igen.

### <a name="can-i-register-the-server-to-multiple-vaults"></a>Regisztrálhatom a kiszolgálót több tárba?

Nem. Egy DPM vagy Azure Backup-kiszolgáló csak egy tárolóban regisztrálható.

### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>Használhatom a DPM az alkalmazások biztonsági mentését Azure Stack-ben?

Nem. A Azure Backup használatával biztosíthatja a Azure Stackt, Azure Backup nem támogatja a DPM használatát az alkalmazások Azure Stack-beli biztonsági mentéséhez.

### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>Ha telepítettem Azure Backup ügynököt a fájlok és mappák védeleméhez, telepíthetem a System Center DPM-t a helyszíni számítási feladatok Azure-ba való biztonsági mentésére?

Igen. Először is be kell állítania a DPM, majd telepítenie kell a Azure Backup ügynököt.  Az összetevők ebben a sorrendben történő telepítése biztosítja, hogy a Azure Backup ügynök együttműködik a DPM. Az ügynök telepítése a DPM telepítése előtt nem javasolt vagy nem támogatott.

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>Miért nem lehet külső DPM-kiszolgálót felvenni a HASZNÁLNA 7 és a legújabb Azure Backup ügynök telepítése után?

A felhőben védett adatforrásokkal rendelkező DPM-kiszolgálók esetében (a 7. kumulatív frissítésnél korábbi kumulatív frissítés használatával) a HASZNÁLNA 7 és a legújabb Azure Backup ügynök telepítése után meg kell várnia a **külső DPM-kiszolgáló hozzáadásának** megkezdését. Az egynapos időszak szükséges ahhoz, hogy feltöltse a DPM védelmi csoportok metaadatait az Azure-ba. A védelmi csoport metaadatait a rendszer első alkalommal feltölti egy éjszakai feladattal.

### <a name="are-there-recommendations-for-configuring-exclusions-for-antivirus-software"></a>Vannak javaslatok a víruskereső szoftverek kizárásának konfigurálására?

Igen, javasoljuk a víruskeresők kizárásának konfigurálását. A DPM kizárásával kapcsolatban lásd: [víruskereső szoftver futtatása a DPM-kiszolgálón](/system-center/dpm/run-antivirus-server). A MABS kizárásával kapcsolatban lásd: [a MABS-kiszolgáló víruskereső konfigurálása](backup-azure-mabs-troubleshoot.md#configure-antivirus-for-mabs-server).

## <a name="vmware-and-hyper-v-backup"></a>VMware és Hyper-V biztonsági mentés

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Tudok biztonsági mentést készíteni VMware vCenter-kiszolgálókról az Azure-ban?

Igen. A Azure Backup Server használatával biztonsági mentést készíthet VMware vCenter Server és ESXi-gazdagépekről az Azure-ba.

- [További](backup-mabs-protection-matrix.md) információ a támogatott verziókról.
- A VMware-kiszolgáló biztonsági mentését a következő [lépésekkel](backup-azure-backup-server-vmware.md) végezheti el.

### <a name="do-i-need-a-separate-license-to-recover-a-full-on-premises-vmwarehyper-v-cluster"></a>Külön licencre van szükségem egy teljes helyszíni VMware/Hyper-V fürt helyreállításához?

Nincs szükség külön licencelésre a VMware/Hyper-V-védelemhez.

- Ha Ön a System Center-ügyfél, használja a System Center Data Protection Manager (DPM) szolgáltatást a VMware virtuális gépek elleni védelemhez.
- Ha nem a System Center-ügyfél, használhatja a Azure Backup Server (utólagos elszámolású) lehetőséget a VMware virtuális gépek elleni védelemhez.

### <a name="can-i-restore-a-backup-of-a-hyper-v-or-vmware-vm-stored-in-azure-to-azure-as-an-azure-vm"></a>Vissza lehet állítani egy Hyper-V vagy VMware virtuális gép biztonsági másolatát az Azure-ban tárolt Azure-beli virtuális gépekre?

Nem, ez jelenleg nem lehetséges. Csak helyszíni gazdagépre lehet visszaállítani.

## <a name="sharepoint"></a>SharePoint

### <a name="can-i-recover-a-sharepoint-item-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson-with-protection-on-disk"></a>Helyreállítható egy SharePoint-elem az eredeti helyre, ha a SharePoint az SQL AlwaysOn használatával van konfigurálva (a lemezes védelemmel)?

Igen, az elem visszaállítható az eredeti SharePoint-webhelyre.

### <a name="can-i-recover-a-sharepoint-database-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson"></a>Helyre lehet állítani egy SharePoint-adatbázist az eredeti helyre, ha a SharePoint az SQL AlwaysOn használatával van konfigurálva?

Mivel a SharePoint-adatbázisok konfigurálva vannak az SQL-AlwaysOn, csak akkor módosíthatók, ha a rendelkezésre állási csoport el lett távolítva. Ennek eredményeképpen a DPM nem tudja visszaállítani az adatbázist az eredeti helyére. SQL Server-adatbázist egy másik SQL Server-példányra is helyreállíthatja.

## <a name="next-steps"></a>Következő lépések

További gyakori kérdéseket is áttekinthet:

- [További](backup-support-matrix-mabs-dpm.md) információ a Azure Backup Server és a DPM támogatási mátrixáról.
- [További](backup-azure-mabs-troubleshoot.md) információ a Azure Backup Server és a DPM hibaelhárítási irányelveiről.
