---
title: Bevezetés a Azure File Sync | Microsoft Docs
description: Az Azure File Sync szolgáltatás áttekintése, amellyel hálózati fájlmegosztásokat hozhat létre és használhat a felhőben az iparági szabvány SMB protokoll használatával.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 04/19/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: da40783e3d299b0edf27c6d045dbc6dcfc5cf964
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797661"
---
# <a name="what-is-azure-file-sync"></a>Mi az az Azure File Sync?
Azure File Sync lehetővé teszi a szervezet fájlmegosztásának központozását a Azure Files- és fájlkiszolgálókban, miközben biztosítja a Windows-fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását. Bár egyes felhasználók dönthetnek úgy, hogy helyben tosztják ki az adataik teljes másolatát, a Azure File Sync a Windows Servert az Azure-fájlmegosztás gyors gyorsítótárába is átalakíthatja. A Windows Serveren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl.: SMB, NFS vagy FTPS). A világ minden táján annyi gyorsítótárral lehet, amennyire csak szüksége van.   

## <a name="videos"></a>Videók
| A Azure File Sync | Azure Files Sync (Ignite 2019)  |
|-|-|
| [![Az Introducing Azure File Sync videó képernyőképe – kattintson a lejátszáshoz!](../files/media/storage-files-introduction/azure-file-sync-video-snapshot.png)](https://www.youtube.com/watch?v=Zm2w8-TRn-o) | [![Képernyőfelvétel a Azure Files Sync bemutatóval – kattintson a lejátszáshoz!](../files/media/storage-files-introduction/ignite-2018-video.png)](https://www.youtube.com/embed/6E2p28XwovU) |

## <a name="benefits-of-azure-file-sync"></a>A Azure File Sync

### <a name="cloud-tiering"></a>Felhőbeli rétegzés
Ha engedélyezve van a felhőbeli rétegezés, a rendszer a leggyakrabban használt fájlokat a helyi kiszolgálón gyorsítótárazza, a ritkábban használt fájlokat pedig a felhőbe. Szabályozhatja, hogy mennyi helyi lemezterületet használ a gyorsítótárazás. A rétegzett fájlok gyorsan visszahívhatóak igény szerint, így zökkenőmentes a felhasználói élmény, miközben a költségek csökkentése is lehetővé teszi, mivel csak az adatok egy részét kell a helyszínen tárolnia. További információ a felhőbeli rétegezésről: [Felhőbeli rétegezés áttekintése.](file-sync-cloud-tiering-overview.md) 

### <a name="multi-site-access-and-sync"></a>Többhelyű hozzáférés és szinkronizálás
Azure File Sync ideális elosztott hozzáférési forgatókönyvekhez. Minden irodához kiépíthet egy helyi Windows Servert a központi telepítés Azure File Sync részeként. Az egyik irodában található kiszolgálón végrehajtott módosítások automatikusan szinkronizálódnak az összes többi irodában található kiszolgálókkal.

### <a name="business-continuity-and-disaster-recovery"></a>Folyamatos üzletmenet és vészhelyreállítás
Azure File Sync biztonsági Azure Files, amely számos redundancia-lehetőséget kínál a magas rendelkezésre áll rendelkezésre állás érdekében. Mivel az Azure rugalmas másolatokat tartalmaz az adatokról, a helyi kiszolgáló egy egyszer használatos gyorsítótárazó eszköz lesz, és egy meghibásodott kiszolgáló helyreállítása egy új kiszolgálónak az üzemelő példányhoz való hozzáadásával Azure File Sync meg. Helyi biztonsági másolatból való visszaállítás helyett üzembe kell állítania egy másik Windows Servert, telepítenie kell rajta a Azure File Sync-ügynököt, majd hozzá kell adni a Azure File Sync telepítéséhez. Azure File Sync letölti a fájlnévteret az adatok letöltése előtt, hogy a kiszolgáló a lehető leghamarabb futjon. A még gyorsabb helyreállítás érdekében a telepítés részeként kiszolgálói kiszolgálóval is lehet készenléti kiszolgálót használni, vagy használhatja a Azure File Sync windowsos fürtszolgáltatással.

## <a name="cloud-side-backup"></a>Felhőoldali biztonsági mentés
Csökkentheti a helyszíni biztonsági mentések kiadásait azáltal, hogy központosított biztonsági másolatokat készít a felhőben az Azure Backup. Azure Files SMB-megosztások natív pillanatkép-képességekkel rendelkeznek, és a folyamat a Azure Backup segítségével automatizálható a biztonsági mentések ütemezése és a megőrzésük kezelése érdekében. Azure Backup a helyszíni kiszolgálókkal is integrálható, így a felhőbe való visszaállításkor ezek a módosítások automatikusan letöltve vannak a Windows-kiszolgálókra.  

## <a name="next-steps"></a>Következő lépések
* [Az Azure File Sync üzembe helyezésének megtervezése](file-sync-planning.md)
* [Felhőbeli rétegezés áttekintése](file-sync-cloud-tiering-overview.md)
* [Az Azure File Sync monitorozása](file-sync-monitoring.md)