---
title: Válasz az Azure Defender for Resource Manager-riasztásokra
description: Ismerje meg, hogy milyen lépéseket kell megtenni az Azure Defender for Resource Manager riasztására való válaszadáshoz
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 54790795aab8aac247e17198159130d7139dd38c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96754650"
---
# <a name="respond-to-azure-defender-for-resource-manager-alerts"></a>Válaszadás a Resource Managerhez készült Azure Defender-riasztásokra

Ha az Azure Defender for Resource Manager riasztást kap, javasoljuk, hogy vizsgálja meg a riasztást, és válaszoljon az alább leírtak szerint. Az Azure Defender for Resource Manager védi az összes kapcsolódó erőforrást, így még akkor is, ha már ismeri a riasztást kiváltó alkalmazást vagy felhasználót, fontos, hogy ellenőrizze az összes riasztást körülvevő helyzetet.  


## <a name="step-1-contact"></a>1. lépés Kapcsolattartó

1. Forduljon az erőforrás-tulajdonoshoz, és állapítsa meg, hogy a viselkedés várt vagy szándékos volt-e.
1. Ha a tevékenység várható, zárja be a riasztást.
1. Ha a tevékenység váratlan, a kapcsolódó felhasználói fiókokat, előfizetéseket és virtuális gépeket a következő lépésben leírtak szerint kezelheti feltörtként és enyhítve.

## <a name="step-2-immediate-mitigation"></a>2. lépés Azonnali mérséklés 

1. Feltört felhasználói fiókok szervizelése:
    - Ha nem ismerik őket, törölje őket, ahogy azt a fenyegetést tartalmazó színész hozta létre
    - Ha már ismerősek, módosítsa a hitelesítő adatait
    - Az Azure-Tevékenységnaplók segítségével áttekintheti a felhasználó által végrehajtott összes tevékenységet, és azonosíthatja a gyanús

1. Feltört előfizetések szervizelése:
    - Minden ismeretlen Runbookok eltávolítása a feltört Automation-fiókból
    - Tekintse át az előfizetésre vonatkozó IAM-engedélyeket, és távolítsa el az összes ismeretlen felhasználói fiók engedélyeit
    - Tekintse át az összes Azure-erőforrást az előfizetésben, és töröljön minden ismeretlent
    - Tekintse át és vizsgálja meg az előfizetéshez tartozó összes biztonsági riasztást Azure Security Center
    - Az Azure-Tevékenységnaplók segítségével áttekintheti az előfizetésben végrehajtott összes tevékenységet, és azonosíthatja a gyanús

1. A feltört virtuális gépek szervizelése
    - Az összes felhasználó jelszavának módosítása
    - Teljes antimalware-vizsgálat futtatása a gépen
    - A gépek alaphelyzetbe állítása kártevők nélküli forrásból


## <a name="next-steps"></a>Következő lépések

Ez az oldal az Azure Defender for Resource Manager szolgáltatással kapcsolatos riasztásokra adott válasz folyamatát ismerteti. A kapcsolódó információk a következő lapokon találhatók:

- [A Resource Managerhez készült Azure Defender bemutatása](defender-for-resource-manager-introduction.md)
- [Riasztások letiltása az Azure Defenderből](alerts-suppression-rules.md)
- [Security Center-adatfeldolgozás folyamatos exportálása](continuous-export.md)