---
title: Tudnivalók a MARS-ügynökről
description: Ismerje meg, hogyan támogatja a MARS-ügynök a biztonsági mentési forgatókönyveket
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 8e4ace0c17dbe75e989981db56583ed9477b3716
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87562599"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Tudnivalók a Microsoft Azure Recovery Services (MARS) ügynökről

Ez a cikk azt ismerteti, hogyan használja a Azure Backup szolgáltatás a Microsoft Azure Recovery Services (MARS) ügynököt a fájlok, mappák, valamint a kötet vagy a rendszerállapot biztonsági mentésére és visszaállítására egy helyszíni számítógépről az Azure-ba.

## <a name="backup-scenarios"></a>Biztonsági mentési forgatókönyvek

A MARS-ügynök a következő biztonsági mentési forgatókönyveket támogatja:

![MARS biztonsági mentési forgatókönyvek](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **Fájlok és mappák: a** Windows-fájlok és-mappák szelektív védelemmel való ellátása.
- **Kötet szintje**: a számítógép teljes Windows-kötetének a megóvása.
- **Rendszerszintű**: a teljes Windows rendszerállapotának biztosítása.

### <a name="additional-scenarios"></a>További helyzetek

- **Meghatározott fájlok és mappák biztonsági mentése az Azure Virtual Machines** szolgáltatásban: az Azure-beli virtuális gépek (VM-EK) biztonsági mentésének elsődleges módszere egy Azure Backup-bővítmény használata a virtuális gépen. A bővítmény biztonsági másolatot készít a teljes virtuális gépről. Ha meghatározott fájlokról és mappákról szeretne biztonsági mentést készíteni egy virtuális gépen belül, telepítheti a MARS-ügynököt az Azure-beli virtuális gépekre. További információ: [architektúra: beépített Azure virtuális gépek biztonsági mentése](./backup-architecture.md#architecture-built-in-azure-vm-backup).

- **Offline előkészítés**: az Azure-ba irányuló adatok kezdeti teljes biztonsági mentése általában nagy mennyiségű adat átvitelét és nagyobb hálózati sávszélességet igényel. A következő biztonsági másolatok csak a különbözetet, vagy növekményes adatmennyiséget továbbítanak. Azure Backup tömöríti a kezdeti biztonsági mentéseket. Az *Offline kivetés* folyamatán keresztül a Azure Backup lemezek használatával feltölthetik a tömörített kezdeti biztonsági mentési adatok az Azure-ba. További információ: [Azure Backup offline biztonsági mentés Azure Data Box használatával](offline-backup-azure-data-box.md).

## <a name="restore-scenarios"></a>Visszaállítási forgatókönyvek

A MARS-ügynök a következő visszaállítási forgatókönyveket támogatja:

![MARS helyreállítási forgatókönyvek](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **Ugyanazon kiszolgáló**: az a kiszolgáló, amelyen a biztonsági másolatot eredetileg létrehozták.
  - **Fájlok és mappák**: válassza ki a visszaállítani kívánt fájlokat és mappákat.
  - **Kötet szintje**: válassza ki a visszaállítani kívánt kötetet és helyreállítási pontot. Ezután állítsa vissza ugyanarra a helyre vagy egy másik helyre ugyanazon a gépen.  Meglévő fájlok másolatának létrehozása, meglévő fájlok felülírása vagy a meglévő fájlok helyreállításának kihagyása.
  - **Rendszerszintű**: válassza ki a rendszerállapotot és a helyreállítási pontot, hogy a visszaállítás ugyanarra a gépre történjen egy adott helyen.

- **Másodlagos kiszolgáló**: a biztonsági mentést végző kiszolgálótól eltérő kiszolgáló.
  - **Fájlok és mappák**: válassza ki azokat az egyes fájlokat és mappákat, amelyeknek a helyreállítási pontját szeretné visszaállítani a célszámítógépen.
  - **Kötet szintje**: válassza ki azt a kötetet és helyreállítási pontot, amelyet vissza szeretne állítani egy másik helyre. Meglévő fájlok másolatának létrehozása, meglévő fájlok felülírása vagy a meglévő fájlok helyreállításának kihagyása.
  - **Rendszerszintű**: válassza ki a rendszerállapotot és a helyreállítási pontot, ha rendszerállapot-fájlként szeretné visszaállítani egy másik számítógépre.

## <a name="backup-process"></a>A biztonsági mentés folyamata

1. A Azure Portal hozzon létre egy [Recovery Services](install-mars-agent.md#create-a-recovery-services-vault)-tárolót, majd a **biztonsági mentési célokból** válassza a fájlok, mappák és a rendszerállapot lehetőséget.
2. [Töltse le a Recovery Services-tároló hitelesítő adatait és az ügynök telepítőjét](./install-mars-agent.md#download-the-mars-agent) egy helyszíni gépre.

3. [Telepítse az ügynököt](./install-mars-agent.md#install-and-register-the-agent) , és a letöltött tároló hitelesítő adataival regisztrálja a gépet a Recovery Services-tárolóba.
4. A-ügyfél ügynök-konzolján [konfigurálja a biztonsági mentést](./backup-windows-with-mars-agent.md#create-a-backup-policy) , és adja meg a biztonsági mentést, a biztonsági mentés idejét (az ütemezést), a biztonsági másolatok megőrzésének idejét az Azure-ban (az adatmegőrzési szabályzatban), és a védelem megkezdéséhez.

![Azure Backup-ügynök diagramja](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-information"></a>További információ

- A **kezdeti biztonsági** mentés (az első biztonsági mentés) a biztonsági mentési beállításoknak megfelelően fut.  A MARS-ügynök a VSS-t használja a biztonsági mentésre kijelölt kötetek időpontra vonatkozó pillanatképének elkészítéséhez. Az ügynök csak a Windows rendszeríró műveletet használja a pillanatkép rögzítéséhez. Nem használ semmilyen Application VSS-írót, és nem rögzíti az alkalmazás-konzisztens pillanatképeket. A pillanatképnek a VSS-vel való elkészítése után a MARS-ügynök létrehoz egy virtuális merevlemezt (VHD) a biztonsági mentés konfigurálásakor megadott gyorsítótár-mappában. Az ügynök az egyes adatblokkok ellenőrzőösszegeit is tárolja.

- A **növekményes biztonsági mentések** (az azt követő biztonsági másolatok) a megadott ütemezés szerint futnak. A növekményes biztonsági mentések során a módosított fájlok azonosíthatók, és létrejön egy új VHD. A virtuális merevlemez tömörítve és titkosítva van, majd a rendszer elküldje a tárolónak. A növekményes biztonsági mentés befejeződése után az új VHD a kezdeti replikáció után létrehozott VHD-vel lesz egyesítve. Ez az egyesített VHD biztosítja a legújabb, a folyamatban lévő biztonsági mentéshez való összehasonlításhoz használt állapotot.

- A MARS-ügynök **optimalizált módban** futtathatja a biztonsági mentési FELADATOT az USN (frissítési sorszám) módosítási napló használatával, vagy nem optimalizált **módban** , ha a címtárakban vagy fájlokban lévő módosításokat ellenőrzi a teljes kötet vizsgálatával. Az optimalizálatlan mód lassabb, mert az ügynöknek a köteten lévő összes fájlt be kell olvasnia, és össze kell hasonlítani a metaadatokkal a módosított fájlok meghatározásához.  A **kezdeti biztonsági mentés** mindig nem optimalizált módban fog futni. Ha az előző biztonsági mentés sikertelen volt, a következő ütemezett biztonsági mentési feladatokra nem optimalizált módban fog futni. Ha többet szeretne megtudni ezekről a módokról és azok ellenőrzéséről, tekintse meg [ezt a cikket](backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-job-running-in-unoptimized-mode).

## <a name="next-steps"></a>Következő lépések

[A MARS-ügynök támogatási mátrixa](./backup-support-matrix-mars-agent.md)

[MARS-ügynök – gyakori kérdések](./backup-azure-file-folder-backup-faq.md)
