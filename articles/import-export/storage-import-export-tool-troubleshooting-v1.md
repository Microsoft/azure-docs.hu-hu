---
title: Az importálási és exportálási problémák elhárítása az Azure import/export szolgáltatásban | Microsoft Docs
description: Ismerje meg, hogyan kezelheti az Azure import/export szolgáltatással kapcsolatos gyakori problémákat.
author: v-dalc
services: storage
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/19/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 555529b52d586078ba7e1832373ec126ba545c11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98706430"
---
# <a name="troubleshoot-issues-in-azure-importexport"></a>Az Azure import/export szolgáltatással kapcsolatos problémák elhárítása
Ez a cikk az Azure import/export szolgáltatásbeli adatimportálás és-exportálás során felmerülő gyakori hibák elhárítását ismerteti.

## <a name="a-copy-session-failed-what-i-should-do"></a>A másolási munkamenet sikertelen volt. Mit kell tennem?  

Ha egy másolási munkamenet meghiúsul, két lehetőség közül választhat:  
* Ha a hiba újra próbálkozik, például ha a hálózati megosztás rövid ideig offline állapotban van, és most újra online állapotú, akkor folytathatja a másolási munkamenetet.
* Ha a hiba nem próbálkozhat újra – ha például helytelen forrásfájl-könyvtárat adott meg a parancssori paraméterekben, meg kell szakítania a másolási munkamenetet.
 
<!--For information about resuming and aborting copy sessions, see [Preparing Hard Drives for an Import Job](../storage-import-export-tool-preparing-hard-drives-import-v1.md  - Article we removed from TOC. File remains.-->

## <a name="i-cant-resume-or-abort-a-copy-session"></a>Nem tudom folytatni vagy megszakítani a másolási munkamenetet.

Ha a másolási munkamenet a meghajtó első másolási munkamenete, akkor a hibaüzenetnek meg kell jelennie: "az első másolási munkamenet nem folytatható és nem szakítható meg." Ebben az esetben törölheti a régi naplófájlt, és újra futtathatja a parancsot.  

Ha egy meghajtón nem az első egy másolási munkamenet, akkor a munkamenet mindig folytatható vagy megszakítható.  

## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>Elveszítettem a naplófájlt. Továbbra is létrehozhatom a feladatot?

A meghajtóhoz tartozó naplófájl teljes munkamenet-információt tartalmaz az adatok másolásáról. Amikor fájlokat ad hozzá a meghajtóhoz, a rendszer az importálási feladatok létrehozásához használja a naplófájlt. Ha elveszíti a naplófájlt, a meghajtó összes másolási munkamenetét újra meg kell ismételni.

## <a name="next-steps"></a>Következő lépések

* [Az Azure import/export eszköz beállítása](storage-import-export-tool-setup-v1.md)
* [Merevlemezek előkészítése importálási feladatokhoz](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)
* [A feladatok állapotának áttekintése a másolási naplófájlok között](storage-import-export-tool-reviewing-job-status-v1.md)
* [Importálási feladatok javítása](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Exportálási feladatok javítása](storage-import-export-tool-repairing-an-export-job-v1.md)