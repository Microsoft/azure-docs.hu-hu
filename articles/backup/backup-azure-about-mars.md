---
title: A MARS-ügynökről
description: Ismerje meg, hogyan támogatja a MARS-ügynök a biztonsági mentési forgatókönyveket
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 9e01694aca386482f9ff7ba52593c88326ba3d62
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517742"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Az Microsoft Azure Recovery Services- (MARS-) ügynökről

Ez a cikk azt ismerteti, hogy a Azure Backup-szolgáltatás hogyan használja az Microsoft Azure Recovery Services- (MARS-) ügynököt fájlok, mappák és kötetek és rendszerállapotok biztonsági mentésére és visszaállítására egy helyszíni számítógépről az Azure-ba.

## <a name="backup-scenarios"></a>Biztonsági mentési forgatókönyvek

A MARS-ügynök a következő biztonsági mentési forgatókönyveket támogatja:

![MARS biztonsági mentési forgatókönyvek](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **Fájlok és mappák:** Windows-fájlok és -mappák szelektív védelme.
- **Kötetszint:** A gép teljes Windows-kötetének védelme.
- **Rendszerszint:** Egy teljes Windows rendszerállapot védelme.

### <a name="additional-scenarios"></a>További helyzetek

- **Adott fájlok és** mappák biztonsági mentése az Azure-beli virtuális gépeken: Az Azure-beli virtuális gépek (VM-ek) biztonsági mentése során az elsődleges módszer egy Azure Backup bővítmény használata a virtuális gépen. A bővítmény a teljes virtuális gépről biztonsági ment. Ha egy virtuális gépen belül bizonyos fájlokról és mappákról szeretne biztonsági mentéset, telepítheti a MARS-ügynököt az Azure-beli virtuális gépeken. További információ: [Architektúra: Beépített Azure-beli virtuális gép biztonsági mentése.](./backup-architecture.md#architecture-built-in-azure-vm-backup)

- **Offline átvitel:** Az adatok kezdeti teljes biztonsági mentése az Azure-ba általában nagy mennyiségű adatot továbbít, és nagyobb hálózati sávszélességet igényel. A későbbi biztonsági mentések csak a különbözeti vagy növekményes adatmennyiséget továbbítják. Azure Backup a kezdeti biztonsági mentéseket tömöríti. Az offline töltés *folyamata* során a Azure Backup lemezeket használhat a tömörített kezdeti biztonsági mentési adatok offline feltöltéséhez az Azure-ba. További információ: offline [Azure Backup biztonsági mentés Azure Data Box.](offline-backup-azure-data-box.md)

## <a name="restore-scenarios"></a>Visszaállítási forgatókönyvek

A MARS-ügynök a következő visszaállítási forgatókönyveket támogatja:

![A MARS helyreállítási forgatókönyvei](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **Ugyanaz a kiszolgáló:** Az a kiszolgáló, amelyen a biztonsági mentést eredetileg létrehozták.
  - **Fájlok és mappák:** Válassza ki a visszaállítani kívánt fájlokat és mappákat.
  - **Kötetszint:** Válassza ki a visszaállítani kívánt kötetet és helyreállítási pontot. Ezután állítsa vissza ugyanannak a helynek vagy egy másik helynek ugyanazon a gépen.  Másolatot készíthet a meglévő fájlokról, felülírhatja a meglévő fájlokat, vagy kihagyhatja a meglévő fájlok helyreállítását.
  - **Rendszerszint:** Válassza ki a rendszerállapotot és a helyreállítási pontot, hogy egy adott helyen visszaállítsa ugyanannak a gépnek a visszaállítását.

- **Alternatív kiszolgáló:** Egy másik kiszolgáló, amely nem az a kiszolgáló, amelyről a biztonsági mentés készült.
  - **Fájlok és mappák:** Válassza ki azokat a fájlokat és mappákat, amelyeknek helyreállítási pontját vissza szeretné állítani a célgépre.
  - **Kötetszint:** Válassza ki a másik helyre visszaállítani kívánt kötetet és helyreállítási pontot. Másolatot készíthet a meglévő fájlokról, felülírhatja a meglévő fájlokat, vagy kihagyhatja a meglévő fájlok helyreállítását.
  - **Rendszerszint:** Válassza ki a rendszerállapotot és a helyreállítási pontot, hogy rendszerállapot-fájlként egy másik gépre állítsa vissza.

## <a name="backup-process"></a>A biztonsági mentés folyamata

1. A Azure Portal hozzon létre egy [Recovery Services-tárolót,](install-mars-agent.md#create-a-recovery-services-vault)és válasszon fájlokat, mappákat és rendszerállapotot a **Biztonsági mentés céljai közül.**
2. [Töltse le a Recovery Services-tároló hitelesítő adatait és ügynöktelepítőjét](./install-mars-agent.md#download-the-mars-agent) egy helyszíni gépre.

3. [Telepítse az ügynököt,](./install-mars-agent.md#install-and-register-the-agent) és a letöltött tároló hitelesítő adataival regisztrálja a gépet a Recovery Services-tárolóban.
4. Az ügyfél ügynökkonzolján [](./backup-windows-with-mars-agent.md#create-a-backup-policy) konfigurálja a biztonsági mentést annak megadásához, hogy miről, mikor (az ütemezésről), mennyi ideig kell megőrizni a biztonsági másolatokat az Azure-ban (a megőrzési házirendet), és mikor kezdje meg a védelmet.

![Azure Backup ügynök ábrája](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-information"></a>További információ

- A **kezdeti biztonsági mentés** (első biztonsági mentés) a biztonsági mentési beállításoknak megfelelően fut.  A MARS-ügynök a VSS-t használja a biztonsági mentéshez kiválasztott kötetek időponthoz készült pillanatképének készítéséhez. Az ügynök csak a Windows rendszeríró műveletet használja a pillanatkép rögzítéséhez. Nem használ alkalmazás VSS-írókat, és nem rögzít alkalmazás-konzisztens pillanatképeket. Miután a VSS-sel készített pillanatképet, a MARS-ügynök létrehoz egy virtuális merevlemezt (VHD) a biztonsági mentés konfigurálásakor megadott gyorsítótármappában. Az ügynök az egyes adatblokkok ellenőrzőanyagát is tárolja.

- **A növekményes** biztonsági mentések (későbbi biztonsági mentések) a megadott ütemezés szerint futnak. A növekményes biztonsági mentések során a rendszer azonosítja a módosított fájlokat, és létrehoz egy új virtuális merevlemezt. A VHD tömörítve és titkosítva van, majd a tárolóba lesz elküldve. A növekményes biztonsági mentés befejezése után az új virtuális merevlemez egyesül a kezdeti replikáció után létrehozott virtuális merevlemezsel. Ez az egyesített VHD a legújabb állapotot biztosítja a folyamatban lévő biztonsági mentések összehasonlításához.

- A MARS-ügynök futtathatja  a biztonsági mentési feladatot optimalizált módban az USN  (Frissítési sorszám) módosítási naplóval, vagy nem optimalizált módban a könyvtárak vagy fájlok változásainak ellenőrzéséhez a teljes kötet vizsgálatával. Az optimalizált mód lassabb, mert az ügynöknek be kell olvasnia a köteten lévő összes fájlt, és össze kell hasonlítani a metaadatokkal a módosított fájlok meghatározásához.  A **kezdeti biztonsági** mentés mindig nem optimalizált módban fog futni. Ha az előző biztonsági mentés sikertelen volt, akkor a következő ütemezett biztonsági mentési feladat nem optimalizált módban fog futni. Ha többet szeretne megtudni ezekről a módokról és az ellenőrzésükről, tekintse meg [ezt a cikket.](backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-job-running-in-unoptimized-mode)

## <a name="next-steps"></a>Következő lépések

[A MARS-ügynök támogatási mátrixa](./backup-support-matrix-mars-agent.md)

[MARS-ügynök – gyakori kérdések](./backup-azure-file-folder-backup-faq.yml)
