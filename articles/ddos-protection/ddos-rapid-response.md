---
title: Azure DDoS – gyors reagálás
description: Ismerje meg, hogyan vehet fel DDoS-szakértőket aktív támadás keretében a speciális támogatás érdekében.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: yitoh
ms.openlocfilehash: 8e860bf47420f2b58c44df695da7761bcc2aa0ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100521781"
---
# <a name="azure-ddos-rapid-response"></a>Azure DDoS – gyors reagálás

Aktív hozzáférés esetén Azure DDoS Protection a standard szintű ügyfelek hozzáférhetnek a DDoS Rapid Response (DRR) csapathoz, amely a támadás és a támadás utáni elemzés során segíthet a támadási vizsgálatban.

## <a name="prerequisites"></a>Előfeltételek

- Az oktatóanyag lépéseinek elvégzéséhez először létre kell hoznia egy [Azure DDoS standard Protection-csomagot](manage-ddos-protection.md).

## <a name="when-to-engage-drr"></a>Mikor kell DRR

A DRR csak akkor folytassa, ha: 

- A DDoS-támadások során, ha úgy látja, hogy a védett erőforrás teljesítménye súlyosan csökken, vagy az erőforrás nem érhető el. 
- Úgy gondolja, hogy az erőforrást DDoS-támadás érte, de DDoS Protection szolgáltatás nem hárítja el megfelelően a támadást.
- Olyan népszerű eseményt tervez, amely jelentősen növelni fogja a hálózati forgalmat.
- Kritikus üzleti hatású támadások esetén.

## <a name="engage-drr-during-an-active-attack"></a>DRR bevonása aktív támadás során

1. Azure Portal egy új támogatási kérelem létrehozásakor válassza a **probléma típusa** technikai lehetőséget.
2. Válassza a **szolgáltatás** a **DDoS Protection** lehetőséget.
3. Válasszon ki egy erőforrást az erőforrás legördülő menüben. _Ki kell választania egy DDoS-csomagot, amely a DDoS Protection standard által védett virtuális hálózathoz kapcsolódik a DRR bevonásához._

    ![Erőforrás kiválasztása](./media/ddos-rapid-response/choose-resource.png)

4. A következő **probléma** lapon válassza ki a **súlyosságot** a kritikus hatás és a **probléma típusaként** "a támadás alatt" értékre.

    ![PSeverity és a probléma típusa](./media/ddos-rapid-response/severity-and-problem-type.png)

5. Fejezze be a további részleteket, és küldje el a támogatási kérelmet.

A DRR követi a Azure Rapid Response támogatási modellt. A gyors reagálással kapcsolatos további információkért tekintse meg a [támogatás hatókörét és a válaszadást](https://azure.microsoft.com/en-us/support/plans/response/) ismertető témakört.

További információért olvassa el a [DDoS Protection standard dokumentációt](./ddos-protection-overview.md).

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [tesztelheti a szimulációkat](test-through-simulations.md).
- Megtudhatja [, hogyan tekintheti meg és konfigurálhatja a DDoS Protection telemetria](telemetry.md).
- Megtudhatja, hogyan [tekintheti meg és konfigurálhatja a DDoS diagnosztikai naplózást](diagnostic-logging.md).
