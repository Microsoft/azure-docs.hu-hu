---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 11/09/2018
ms.author: vlvinogr
ms.openlocfilehash: 2bfa356deeede1c16bd5a464ea7081132a67faf6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96183826"
---
## <a name="append-other-apis"></a>Egyéb API-k hozzáfűzése

Az API-k tartalmazhatnak különböző szolgáltatások által elérhetővé tett API-kat, beleértve az OpenAPI-specifikációt, egy SOAP API-t, valamint az Azure App Service, az Azure Function App, az Azure Logic Apps és az Azure Service Fabric API Apps funkcióját.

![API importálása](./media/api-management-append-apis/import.png)

A következő lépésekkel fűzhet hozzá egy meglévő API-hoz egy másik API-t. Egy másik API importálásakor a műveletek hozzáfűződnek az aktuális API-hoz.

1. Nyissa meg az Azure API Management-példányát az Azure Portalon.
2. A bal oldali menüből válassza ki az **API-k** elemet.
3. Kattintson a **...** elemre azon API mellett, amelyhez egy másik API-t szeretne hozzáfűzni.
4. Válassza az **Importálás** elemet a legördülő menüből.
5. Válasszon egy szolgáltatást, amelyből importálni fogja az API-t.