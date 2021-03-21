---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 961762559ba27deee519ad3a9516bc351ca724f7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601353"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A megosztott irányítópultok nem rendelkezhetnek beágyazott tartalommal rendelkező Markdown csempével](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |Nem lehet olyan megosztott irányítópultot létrehozni, amely beágyazott tartalmat tartalmaz a Markdown csempék számára, és azt kényszeríti, hogy a tartalmat a rendszer az online Markdown-fájlként tárolja. Ha beágyazott tartalmat használ a Markdown csempén, akkor nem kezelheti a tartalom titkosítását. A saját tároló konfigurálásával titkosíthatja, megduplázhatja a titkosítást, és akár saját kulcsokat is használhat. A házirend engedélyezése korlátozza a felhasználókat, hogy a megosztott irányítópultok 2020-09-01-es vagy újabb verzióját használják REST API. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
