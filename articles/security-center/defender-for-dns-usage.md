---
title: Válaszadás az Azure Defender DNS-riasztásokra
description: Ismerje meg a DNS-hez készült Azure Defender-riasztásokra való válaszadáshoz szükséges lépéseket
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 375a81127a000741ca5e0397642800794610bcda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96754671"
---
# <a name="respond-to-azure-defender-for-dns-alerts"></a>Válaszadás a DNS-hez készült Azure Defender-riasztásokra

Ha riasztást kap a DNS-hez készült Azure Defendertől, javasoljuk, hogy vizsgálja meg a riasztást, és válaszoljon az alább leírtak szerint. A DNS-hez készült Azure Defender védi az összes kapcsolódó erőforrást, így még akkor is, ha ismeri a riasztást kiváltó alkalmazást vagy felhasználót, fontos ellenőrizni az összes riasztást körülvevő helyzetet.  


## <a name="step-1-contact"></a>1. lépés Kapcsolattartó

1. Forduljon az erőforrás-tulajdonoshoz, és állapítsa meg, hogy a viselkedés várt vagy szándékos volt-e.
1. Ha a tevékenység várható, zárja be a riasztást.
1. Ha a tevékenység váratlan, akkor a következő lépésben leírtak szerint kezelheti az erőforrást potenciálisan feltörtként és enyhítve.

## <a name="step-2-immediate-mitigation"></a>2. lépés Azonnali mérséklés 

1. Különítse el az erőforrást a hálózatról az oldalirányú mozgás megakadályozása érdekében.
1. Futtasson egy teljes antimalware-vizsgálatot az erőforráson, az eredményül kapott szervizelési tanácsokat követve.
1. Tekintse át a telepített és futó szoftvereket az erőforráson, és távolítsa el az ismeretlen vagy nemkívánatos csomagokat.
1. Visszaállíthatja a gépet egy ismert jó állapotba, újratelepítheti az operációs rendszert, ha szükséges, és visszaállíthatja a szoftvereket egy ellenőrzött kártevő-mentes forrásból.
1. Javítsa ki a gép Azure Security Center javaslatait, és szervizelését a jövőbeli jogsértések megelőzése érdekében kiemelt biztonsági problémákat.


## <a name="next-steps"></a>Következő lépések

Ez az oldal az Azure Defender for DNS szolgáltatással kapcsolatos riasztásra adott válasz folyamatát ismerteti. A kapcsolódó információk a következő lapokon találhatók:

- [A DNS-hez készült Azure Defender bemutatása](defender-for-dns-introduction.md)
- [Riasztások letiltása az Azure Defenderből](alerts-suppression-rules.md)
- [Security Center-adatfeldolgozás folyamatos exportálása](continuous-export.md)