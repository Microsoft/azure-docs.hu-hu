---
title: A Azure NetApp Files pillanatkép-házirendjeinek hibáinak megoldása | Microsoft Docs
description: Azokat a hibaüzeneteket és megoldásokat ismerteti, amelyek segítséget nyújtanak a pillanatkép-házirendek kezelési problémáinak elhárításában Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/23/2020
ms.author: b-juche
ms.openlocfilehash: 6ba8b18876bdae2754a6a772ce3909ff2f5a71b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91651004"
---
# <a name="troubleshoot-snapshot-policies"></a>Pillanatkép-szabályzatokkal kapcsolatos problémák elhárítása

Ez a cikk a Azure NetApp Files pillanatkép-szabályzatok kezelése során felmerülő hibákra vonatkozó forgatókönyveket ismerteti. Emellett olyan megoldásokat is biztosít, amelyek segíthetnek a problémák megoldásában.

## <a name="error-conditions-and-resolutions"></a>Hibákra vonatkozó feltételek és megoldások 

|     Hibafeltétel    |     Feloldás    |
|-|-|
| A pillanatkép-szabályzat létrehozása sikertelen, mert a pillanatkép-szabályzat neve érvénytelen. | Hiba történik a pillanatkép-szabályzat létrehozásakor, ha a pillanatkép-házirend neve érvénytelen. A pillanatkép-szabályzatok neveire a következő irányelvek érvényesek:  <ul><li> A pillanatkép-szabályzat neve nem tartalmazhat nem ASCII vagy speciális karaktereket. </li> <li> A pillanatkép-szabályzat nevének betűvel vagy számmal kell kezdődnie, és csak betűket, számokat, aláhúzást (_) és kötőjeleket (-) tartalmazhat. </li> <li> A pillanatkép-szabályzat nevének 1 és 64 karakter közöttinek kell lennie.  </li></ul> Módosítsa a pillanatkép-szabályzat nevét az irányelvek alapján.  |
| A pillanatkép-szabályzat létrehozása érvénytelen értékekkel meghiúsul. | A Azure NetApp Files nem tud pillanatkép-szabályzatot létrehozni, ha érvénytelen értéket ad meg egy mezőhöz, például `Number of snapshots to keep` vagy `Minute on the hour to take snapshot` . Az érvényes értékek a következők:  <ul><li>Az értéknek érvényes számnak kell lennie.</li> <li>Az értéknek 0 és 59 közöttinek kell lennie.</li></ul> Győződjön meg arról, hogy a mezőkhöz érvényes érték van megadva. | 
| A pillanatkép-szabályzat létrehozása sikertelen a következő hibával: `Total number of snapshots to keep exceeds 255` . | Minden kötet [legfeljebb 255 pillanatképet](azure-netapp-files-resource-limits.md)tartalmazhat. A maximális érték az óránkénti, napi, heti és havi Pillanatképek összegét tartalmazza. <br> Csökkentse az `Snapshots to keep` értéket, és próbálkozzon újra. |
| A házirend egy kötethez való hozzárendelésének hibája sikertelen `Total snapshot policy is over the max '255'` . | Minden kötet [legfeljebb 255 pillanatképet](azure-netapp-files-resource-limits.md)tartalmazhat. Ha az igény szerinti, óránkénti, napi, heti és havi Pillanatképek összege meghaladja a maximális értéket, hiba történik. <br> Csökkentse az `snapshots to keep` értéket, vagy töröljön néhány igény szerinti pillanatképet, és próbálkozzon újra. | 

## <a name="next-steps"></a>Következő lépések  

* [Pillanatkép-házirendek kezelése](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)
