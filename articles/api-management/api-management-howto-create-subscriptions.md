---
title: Előfizetések létrehozása az Azure API Managementban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre előfizetéseket az Azure API Managementban.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: f8b2238eb0fab9aeeb42d11b4176c0d681b5f8e5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "70073540"
---
# <a name="create-subscriptions-in-azure-api-management"></a>Előfizetések létrehozása az Azure API Managementben

Amikor API-kat tesz közzé az Azure API Managementon keresztül, az előfizetés-kulcsok használatával egyszerűen és közösen biztosíthatja az API-k elérését. A közzétett API-kat használó ügyfélalkalmazások számára érvényes előfizetési kulcsot kell tartalmazniuk a HTTP-kérelmekben, amikor hívásokat kezdeményeznek az API-khoz. Az API-k eléréséhez szükséges előfizetési kulcs beszerzéséhez előfizetésre van szükség. Az előfizetésekkel kapcsolatos további információkért lásd: [előfizetések az Azure API Managementban](api-management-subscriptions.md).

Ez a cikk végigvezeti az előfizetések Azure Portalban történő létrehozásának lépésein.

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett lépések végrehajtásához az előfeltételek a következők:

+ [Hozzon létre egy API Management példányt](get-started-create-service-instance.md).
+ [Az előfizetések értelmezése API Managementban](api-management-subscriptions.md).

## <a name="create-a-new-subscription"></a>Új előfizetés létrehozása

1. Válassza az **előfizetések** lehetőséget a bal oldali menüben.
2. Válassza az **előfizetés hozzáadása**lehetőséget.
3. Adja meg az előfizetés nevét, és válassza ki a hatókört.
4. Ha szeretné, válassza ki, hogy az előfizetést társítani kell-e egy felhasználóhoz.
5. Kattintson a **Mentés** gombra.

![Rugalmas előfizetések](./media/api-management-subscriptions/flexible-subscription.png)

Az előfizetés létrehozása után a rendszer két API-kulcsot biztosít az API-k eléréséhez. Az egyik kulcs elsődleges, és az egyik a másodlagos. 

## <a name="next-steps"></a>További lépések
További információ a API Managementról:

+ Ismerje meg a API Management egyéb [fogalmait](api-management-terminology.md) .
+ A API Management megismeréséhez kövesse az [oktatóanyagokat](import-and-publish.md) .
+ Gyakori kérdésekért tekintse meg a [gyakran ismételt kérdések oldalát](api-management-faq.md) .