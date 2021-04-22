---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d10090954162bdf614234d2bce863a0d9a1635af
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875726"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A megosztott irányítópultok nem tartalmaznak beágyazott tartalommal és Markdown-csempéket](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |Nem lehet olyan megosztott irányítópultot létrehozni, amely beágyazott tartalommal rendelkezik Markdown-csempéken, és kikényszeríteni, hogy a tartalmat online tárolt Markdown-fájlként tárolja a rendszer. Ha beágyazott tartalmat használ a Markdown-csempén, nem kezelheti a tartalom titkosítását. A saját tárhely konfigurálásával titkosíthatja, duplázhatja a titkosítást, és akár saját kulcsokat is használhatja. A szabályzat engedélyezése arra korlátozza a felhasználókat, hogy a megosztott irányítópultok 2020. 09. 01. 01- vagy újabb verzióját használják REST API. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
