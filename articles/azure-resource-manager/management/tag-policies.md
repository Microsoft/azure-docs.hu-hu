---
title: Erőforrások címkézésére vonatkozó szabályzatok
description: Ismerteti azokat az Azure-szabályzatokat, amelyeket hozzárendelhet a címke megfelelőségének biztosításához.
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: c6867bc01306ac3c08a9797ece0567a45e060af2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89293735"
---
# <a name="assign-policies-for-tag-compliance"></a>Szabályzatok kiosztása a címkézési megfelelőséghez

A [Azure Policy](../../governance/policy/overview.md) a címkézési szabályok és konvenciók betartatására használható. A szabályzat létrehozásával elkerülhető, hogy az előfizetéshez tartozó erőforrások milyen helyzetben legyenek, és hogy ne legyenek a szervezete számára várt címkék. A címkék manuális alkalmazása vagy a nem megfelelő erőforrások keresése helyett hozzon létre egy szabályzatot, amely automatikusan alkalmazza a szükséges címkéket az üzembe helyezés során. A címkék mostantól a meglévő erőforrásokra is alkalmazhatók az új [módosítási](../../governance/policy/concepts/effects.md#modify) effektussal és [szervizelési feladattal](../../governance/policy/how-to/remediate-resources.md). A következő szakasz példákat mutat be a címkékre.

## <a name="policies"></a>Házirendek

[!INCLUDE [Tag policies](../../../includes/policy/reference/bycat/policies-tags.md)]

## <a name="next-steps"></a>Következő lépések

* Az erőforrások címkézésével kapcsolatos további tudnivalókért lásd: [címkék használata az Azure-erőforrások rendszerezéséhez](tag-resources.md).
* Nem minden erőforrástípus támogatja a címkéket. Annak megállapításához, hogy lehet-e címkét alkalmazni az erőforrás típusára, tekintse meg [Az Azure-erőforrások támogatásának címkézését](tag-support.md)ismertető témakört.
