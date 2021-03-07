---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4d419d8e338228132a4aa8a32b7e29d8fa2f46ca
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425454"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A megosztott irányítópultok nem rendelkezhetnek beágyazott tartalommal rendelkező Markdown csempével](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |Nem lehet olyan megosztott irányítópultot létrehozni, amely beágyazott tartalmat tartalmaz a Markdown csempék számára, és azt kényszeríti, hogy a tartalmat a rendszer az online Markdown-fájlként tárolja. Ha beágyazott tartalmat használ a Markdown csempén, akkor nem kezelheti a tartalom titkosítását. A saját tároló konfigurálásával titkosíthatja, megduplázhatja a titkosítást, és akár saját kulcsokat is használhat. A házirend engedélyezése korlátozza a felhasználókat, hogy a megosztott irányítópultok 2020-09-01-es vagy újabb verzióját használják REST API. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
