---
author: v-amallick
ms.service: Azure Backup
ms.topic: include
ms.date: 04/16/2021
ms.author: v-amallick
ms.openlocfilehash: e7af0819a94661a1008d96b7d0738c30a4a80c18
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107716745"
---
1. A biztonsági mentésre kiválasztott Azure-beli virtuális gépek Azure Backup a megadott ütemezés szerint elindítja a biztonsági mentési feladatot.
1. Az első biztonsági mentés során egy biztonsági mentési bővítmény lesz telepítve a virtuális gépre, ha a virtuális gép fut.
    - Windows rendszerű virtuális gépeken a [VMSnapshot bővítmény](../articles/virtual-machines/extensions/vmsnapshot-windows.md) van telepítve.
    - Linux rendszerű virtuális gépeken a [VMSnapshotLinux bővítmény van](../articles/virtual-machines/extensions/vmsnapshot-linux.md) telepítve.
1. Futó Windows rendszerű virtuális gépek esetén a Biztonsági mentés a Windows Kötet árnyékmásolata szolgáltatás (VSS) használatával koordinálja a virtuális gép alkalmazás-konzisztens pillanatképének készítését.
    - Alapértelmezés szerint a biztonsági mentés teljes VSS-biztonsági mentést készít.
    - Ha a Biztonsági mentés nem tud alkalmazás-konzisztens pillanatképet készíteni, akkor fájl konzisztens pillanatképet készít a mögöttes tárolóról (mivel nem történik alkalmazásírás a virtuális gép leállított példánya közben).
1. Linux rendszerű virtuális gépeken a Biztonsági mentés fájl konzisztens biztonsági mentést készít. Az alkalmazás-konzisztens pillanatképek használata előtt és után manuálisan kell testreszabnia a szkripteket.
1. Miután a Biztonsági mentés lehozta a pillanatképet, továbbítja az adatokat a tárolóba.
    - A biztonsági mentés úgy van optimalizálva, hogy párhuzamosan készít biztonsági másolatot az egyes virtuálisgép-lemezekről.
    - Az Azure Backup minden mentett lemez esetében kiolvassa a lemezen lévő blokkokat, majd azonosítja az utolsó biztonsági mentés óta módosult adatblokkokat (a deltát), és csak azokat másolja át.
    - A pillanatképek adatai nem mindig vannak azonnal a helyreállítási tárba másolva. Csúcsidőben órákat is igénybe vehet. Egy virtuális gép biztonsági mentésének időtartama a napi biztonsági mentési szabályzatok esetében 24 óránál kevesebb.
1. A Windows rendszerű virtuális gépeken a Azure Backup után végrehajtott módosítások a következőek:
    - Microsoft Visual C++ 2013 Redistributable(x64) – 12.0.40660 telepítve van a virtuális gépen
    - A Kötet árnyékmásolata szolgáltatás (VSS) indítási típusa manuálisról automatikusra változott
    - Az IaaSVmProvider Windows-szolgáltatás hozzáadva

1. Az adatátvitel befejezésekor a pillanatkép el lesz távolítva, és létrejön egy helyreállítási pont.

![Azure-beli virtuális gépek biztonsági mentési architektúrája](../articles/backup/media/backup-azure-vms-introduction/vmbackup-architecture.png)
