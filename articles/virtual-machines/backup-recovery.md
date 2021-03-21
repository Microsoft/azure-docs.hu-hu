---
title: A virtuális gépek áttekintő biztonsági mentési lehetőségei
description: Az Azure-beli virtuális gépekre vonatkozó biztonsági mentési lehetőségek áttekintése.
author: cynthn
ms.service: virtual-machines
ms.subservice: recovery
ms.topic: conceptual
ms.date: 8/03/2020
ms.author: cynthn
ms.openlocfilehash: c4116ef8d02bd47d6375371be9381553f8c22eda
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565444"
---
# <a name="backup-and-restore-options-for-virtual-machines-in-azure"></a>Virtuális gépek biztonsági mentési és visszaállítási lehetőségei az Azure-ban

Adatai védelme érdekében érdemes rendszeres időközönként biztonság mentést végeznie. A virtuális gépekhez több biztonsági mentési lehetőség is rendelkezésre áll, a használati esettől függően.

## <a name="azure-backup"></a>Azure Backup

Éles számítási feladatokat futtató Azure-beli virtuális gépek biztonsági mentéséhez használja a Azure Backup. A Azure Backup támogatja a Windows és a Linux rendszerű virtuális gépekhez készült alkalmazás-konzisztens biztonsági mentéseket. Az Azure Backup georedundáns helyreállítási tárolókban tárolt helyreállítási pontokat hoz létre. Helyreállítási pontról történő visszaállításkor visszaállíthatja a teljes virtuális gépet, vagy csak bizonyos fájlokat. 

Az Azure-beli virtuális gépek Azure Backupának egyszerű, gyakorlati bevezetését az [Azure Backup](../backup/quick-backup-vm-portal.md)gyors útmutatója tartalmazza.

A Azure Backup működéséről a [virtuális gépek biztonsági mentési infrastruktúrájának megtervezése az Azure-ban](../backup/backup-azure-vms-introduction.md) című témakörben olvashat bővebben.


## <a name="azure-site-recovery"></a>Azure Site Recovery

Azure Site Recovery a virtuális gépeket jelentős katasztrófa esetén védi, amikor egy egész régió jelentős természeti katasztrófák vagy széleskörű szolgáltatás-megszakítás miatt kiesést tapasztal. A virtuális gépekhez Azure Site Recovery konfigurálhatja, hogy az alkalmazás a percek alatt egyetlen kattintással helyreállítható legyen. Egy tetszőleges Azure-régióba replikálhat, és nem korlátozódik a párosított régiókra. 

A vész-helyreállítási gyakorlatokat igény szerinti tesztelési feladatátvételsel futtathatja anélkül, hogy az hatással lenne az éles számítási feladatokra vagy a folyamatos replikálásra. Helyreállítási tervek létrehozása a több virtuális gépen futó teljes alkalmazás feladatátvételének és feladat-visszavételének előkészítéséhez. A helyreállítási terv szolgáltatás integrálva van az Azure Automation runbookok.

[A virtuális gépek replikálásával](../site-recovery/azure-to-azure-quickstart.md)kezdheti meg a lépéseket. 

## <a name="managed-snapshots"></a>Felügyelt Pillanatképek 

A fejlesztési és tesztelési környezetekben a pillanatképek gyors és egyszerű lehetőséget biztosítanak a Managed Disks használó virtuális gépek biztonsági mentéséhez. A felügyelt pillanatkép egy felügyelt lemez írásvédett teljes másolata. A pillanatképek függetlenek a forrás lemeztől, és felhasználhatók új felügyelt lemezek létrehozására a virtuális gépek újraépítése érdekében. A számlázás a lemez használt része alapján történik. Ha például létrehoz egy pillanatképet egy felügyelt lemezről, amely 64 GB kiosztott kapacitással rendelkezik, és a tényleges felhasznált adatok mérete 10 GB, a pillanatképet csak a 10 GB-os adatméretre számítjuk fel.  

A pillanatképek létrehozásával kapcsolatos további információkért lásd:

* [Felügyelt lemezként tárolt VHD másolatának létrehozása pillanatképekkel Windows alatt](./windows/snapshot-copy-managed-disk.md)
* [Felügyelt lemezként tárolt VHD másolatának létrehozása Pillanatképek használatával Linuxon](./linux/snapshot-copy-managed-disk.md)



## <a name="next-steps"></a>Következő lépések
Azure Backup kipróbálásához kövesse a [Azure Backup](../backup/quick-backup-vm-portal.md)gyors útmutatót.