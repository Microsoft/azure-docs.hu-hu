---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: rogarana
ms.openlocfilehash: 1387933dc82c07e73b7715d6593238ea8c993e93
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96005321"
---
Az Azure-fájlmegosztás Azure Portal vagy SMB használatával végzett módosításai nem észlelhetők azonnal, és nem replikálódnak, mint a kiszolgálói végpont módosításai. Azure Files még nem rendelkezik módosítási értesítésekkel vagy naplózással, így a fájlok módosításakor nem lehet automatikusan kezdeményezni a szinkronizálási munkamenetet. A Windows Serveren a Azure File Sync a [Windows USN naplózási](/windows/win32/fileio/change-journals) szolgáltatásával automatikusan kezdeményezi a szinkronizálási munkamenetet a fájlok módosításakor.

Az Azure-fájlmegosztás változásainak észleléséhez Azure File Sync rendelkezik egy *változás-észlelési* feladatokkal nevű ütemezett feladatokkal. A változás-észlelési feladatok a fájlmegosztás összes fájlját felsorolják, majd összehasonlítják az adott fájl szinkronizálási verziójával. Ha a változás észlelése feladata meghatározza, hogy a fájlok megváltoztak, Azure File Sync kezdeményez egy szinkronizálási munkamenetet. A változás észlelése feladatot 24 óránként kezdeményezi a rendszer. Mivel a változás-észlelési feladatok az Azure-fájlmegosztás összes fájljának enumerálásával működnek, az észlelési funkció hosszabb időt vesz igénybe, mint a kisebb névterekben. Nagyméretű névterek esetén 24 óránként hosszabb időt vehet igénybe, hogy meghatározza, mely fájlok változtak.

Az Azure-fájlmegosztás által módosított fájlok azonnali szinkronizálásához a **Meghívási AzStorageSyncChangeDetection** PowerShell-parancsmag használatával manuálisan indíthatja el az Azure-fájlmegosztás változásainak észlelését. Ez a parancsmag olyan forgatókönyvekhez készült, amelyekben bizonyos típusú automatizált folyamatok módosítják az Azure-fájlmegosztást, vagy ha a rendszergazda módosítja a módosításokat (például fájlokat és címtárakat helyez át a megosztásba). A végfelhasználók általi változások érdekében javasoljuk, hogy telepítse a Azure File Sync ügynököt egy IaaS virtuális gépre, és a végfelhasználók hozzáférhessenek a fájlmegosztást a IaaS virtuális gépen keresztül. Így az összes módosítás gyorsan szinkronizálva lesz más ügynökökkel anélkül, hogy az Invoke-AzStorageSyncChangeDetection parancsmagot kellene használnia. További információért lásd a [Meghívási AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) dokumentációját.

>[!NOTE]
>Az Azure-fájlmegosztás REST használatával végzett módosításai nem frissítik az SMB utolsó módosításának időpontját, és nem jelennek meg szinkronizálási változásnak.

A Windows Server rendszerű köteteken található kötetek USN-hez hasonló, a változás észlelését egy Azure-fájlmegosztás esetében vesszük fel. Segítsen nekünk a szolgáltatás rangsorolásában a jövőbeli fejlesztéshez, ha [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)szavaz rá.