---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 04/16/2021
ms.author: vlvinogr
ms.openlocfilehash: 329ea156b296810395eb7b8e8310bed5ee0ee4c9
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107601910"
---
## <a name="append-other-apis"></a>Egyéb API-k hozzáfűzése

Különböző szolgáltatások által elérhetővé téve API-kat is összeállíthat, például:
* Az OpenAPI-specifikáció
* A SOAP API
* A API Apps funkció Azure App Service
* Azure-függvényalkalmazás
* Azure Logic Apps
* Azure Service Fabric

Az alábbi lépésekkel fűz hozzá egy másik API-t a meglévő API-hoz. 

>[!NOTE] 
> Egy másik API importálásakor a műveletek hozzáfűződnek az aktuális API-hoz.

1. Nyissa meg az Azure API Management-példányát az Azure Portalon.

    :::image type="content" source="./media/api-management-append-apis/service-page.png" alt-text="Ugrás az Azure API Mgmt-példányra":::

1. A bal oldali menüből válassza ki az **API-k** elemet.

    :::image type="content" source="./media/api-management-append-apis/api-select.png" alt-text="API-k kiválasztása":::

1. Kattintson a **...** elemre azon API mellett, amelyhez egy másik API-t szeretne hozzáfűzni.
1. Válassza az **Importálás** elemet a legördülő menüből.

    :::image type="content" source="./media/api-management-append-apis/append-01.png" alt-text="Importálás kiválasztása":::

1. Válasszon egy szolgáltatást, amelyből importálni fogja az API-t.

    :::image type="content" source="./media/api-management-append-apis/select-to-import.png" alt-text="Szolgáltatás kiválasztása":::