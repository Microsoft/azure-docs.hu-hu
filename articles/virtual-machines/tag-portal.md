---
title: Virtuális gép címkézése a Azure Portal használatával
description: Ismerje meg, hogyan címkézheti a virtuális gépeket a Azure Portal használatával.
ms.topic: how-to
ms.workload: infrastructure-services
author: cynthn
ms.service: virtual-machines
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: 7c3e779c152a967452f86b55f06d38076102085c
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594978"
---
# <a name="tagging-a-vm-using-the-portal"></a>Virtuális gép címkézése a portál használatával

Ez a cikk azt ismerteti, hogyan adhat hozzá címkéket egy virtuális géphez a portál használatával. A címkék felhasználó által definiált kulcs/érték párok, amelyek közvetlenül egy erőforráson vagy erőforráscsoporton helyezhetők el. Az Azure jelenleg legfeljebb 50 címkét támogat erőforrás és erőforráscsoport szerint. A címkéket a létrehozáskor vagy egy meglévő erőforráshoz való hozzáadáskor lehet elhelyezni egy erőforráson.


1. Navigáljon a virtuális gépre a portálon.
1. Az **alapvető** erőforrások területen válassza a **címkék hozzáadásához kattintson ide**.

    :::image type="content" source="media/tag/azure-portal-tag.png" alt-text="Képernyőfelvétel a virtuális gép oldalának Essentials szakaszáról.":::

1. Adjon meg egy értéket a **név** és az **érték** mezőben, majd válassza a **Mentés** lehetőséget.

    :::image type="content" source="media/tag/key-value.png" alt-text="Képernyőkép az oldalról, amely egy kulcs értékű pár címkével való hozzáadására szolgál.":::



**Következő lépések**

- Az Azure-erőforrások címkézésével kapcsolatos további információkért lásd: [Azure Resource Manager áttekintése](../azure-resource-manager/management/overview.md) és [címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md).
- Ha szeretné megtekinteni, hogyan segíthetnek az Azure-erőforrások használatának kezelésében, tekintse meg [Az Azure-számla megismerése](../cost-management-billing/understand/review-individual-bill.md) és [a Microsoft Azure erőforrás-felhasználás betekintését](../cost-management-billing/manage/usage-rate-card-overview.md)ismertető témakört.