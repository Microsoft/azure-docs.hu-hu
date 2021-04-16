---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e28dc20fe5e789bbaa5aad9402d37f039eabe6e5
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498763"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás(ak) |Verzió<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A megosztott irányítópultok nem tartalmaznak beágyazott tartalmat is, markdown-csempéket](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |Nem lehet olyan megosztott irányítópultot létrehozni, amely beágyazott tartalommal rendelkezik Markdown-csempéken, és kikényszeríteni, hogy a tartalom online tárolt Markdown-fájlként legyen tárolva. Ha beágyazott tartalmat használ a Markdown-csempén, nem kezelheti a tartalom titkosítását. A saját tárhely konfigurálásával titkosíthatja, titkosíthatja és akár saját kulcsait is használhatja. A szabályzat engedélyezésével a felhasználók a megosztott irányítópultok 2020. 09. 01. előzetes vagy újabb verzióját REST API. |Naplózás, Megtagadás, Letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
