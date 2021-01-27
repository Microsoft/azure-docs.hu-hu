---
title: Virtuális gép címkézése a Azure Portal használatával
description: Ismerje meg, hogyan címkézheti a virtuális gépeket a Azure Portal használatával.
ms.topic: how-to
ms.workload: infrastructure-services
author: cynthn
ms.service: virtual-machines
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: 9c220814ae1f714e9eac0c0c11a50d9cf68a621d
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897408"
---
# <a name="tagging-a-vm-using-the-portal"></a>Virtuális gép címkézése a portál használatával

Ez a cikk azt ismerteti, hogyan adhat hozzá címkéket egy virtuális géphez a portál használatával. A címkék felhasználó által definiált kulcs/érték párok, amelyek közvetlenül egy erőforráson vagy erőforráscsoporton helyezhetők el. Az Azure jelenleg legfeljebb 50 címkét támogat erőforrás és erőforráscsoport szerint. A címkéket a létrehozáskor vagy egy meglévő erőforráshoz való hozzáadáskor lehet elhelyezni egy erőforráson.


1. Navigáljon a virtuális gépre a portálon.
1. Az **alapvető** erőforrások területen válassza a **címkék hozzáadásához kattintson ide**.

    :::image type="content" source="media/tag/azure-portal-tag.png" alt-text="Képernyőfelvétel a virtuális gép oldalának Essentials szakaszáról.":::

1. Adjon meg egy értéket a **név** és az **érték** mezőben, majd válassza a **Mentés** lehetőséget.

    :::image type="content" source="media/tag/key-value.png" alt-text="Képernyőkép az oldalról, amely egy kulcs értékű pár címkével való hozzáadására szolgál.":::

### <a name="next-steps"></a>Következő lépések

- Az Azure-erőforrások címkézésével kapcsolatos további információkért lásd: [Azure Resource Manager áttekintése](../azure-resource-manager/management/overview.md) és [címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md).
- Ha szeretné megtekinteni, hogyan segíthet az Azure-erőforrások használatának kezelésében, tekintse meg [Az Azure-számla ismertetése](../cost-management-billing/understand/review-individual-bill.md)című témakört.
