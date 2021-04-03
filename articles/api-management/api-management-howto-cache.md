---
title: Gyorsítótárazás hozzáadása az Azure API Management teljesítményének javításához | Microsoft Docs
description: Megtudhatja, hogyan javíthat az API Management szolgáltatáshívások késleltetésén, sávszélesség-használatán és a webszolgáltatások terhelésén.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: apimpm
ms.openlocfilehash: 732abed830afdb759ed52fd933673edd8e5cade6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94638733"
---
# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>Gyorsítótárazás hozzáadása az Azure API Management teljesítményének javításához

A API Management API-jai és műveletei a válasz gyorsítótárazásával konfigurálhatók. A válasz gyorsítótárazása jelentősen csökkentheti a késést az API-hívók és a háttérbeli terhelések számára az API-szolgáltatók számára.

> [!IMPORTANT]
> A beépített gyorsítótár változékony, és az azonos régióban található összes egység ugyanazon a API Management szolgáltatásban van megosztva.

A gyorsítótárazással kapcsolatos részletes információk: [Az API Management gyorsítótárazási házirendjei](api-management-caching-policies.md) és [Egyedi gyorsítótárazás az Azure API Management szolgáltatásban](api-management-sample-cache-by-key.md).

![gyorsítótár-házirendek](media/api-management-howto-cache/cache-policies.png)

Ismertetett témák:

> [!div class="checklist"]
> * Válaszok gyorsítótárazásának hozzáadása az API esetében
> * A gyorsítótárazás ellenőrzése működés közben

## <a name="availability"></a>Rendelkezésre állás

> [!NOTE]
> A belső gyorsítótár nem érhető el az Azure API Management **felhasználási** szintjében. Ehelyett [egy külső Azure cache-t is használhat a Redis](api-management-howto-cache-external.md) .

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

+ [Azure API Management-példány létrehozása](get-started-create-service-instance.md)
+ [API importálása és közzététele](import-and-publish.md)

## <a name="add-the-caching-policies"></a><a name="caching-policies"> </a>A gyorsítótárazási házirendek hozzáadása

A példában bemutatott gyorsítótárazási házirendek használata esetén a **GetSpeakers** műveletre irányuló első kérés a háttérszolgáltatásból küld vissza választ. Ez a válasz gyorsítótárazva lesz, és egy kulccsal lesz ellátva a megadott fejlécek és lekérdezési sztring paraméterek alapján. A művelet későbbi, egyező paraméterekkel rendelkező hívásai a gyorsítótárazott választ küldik vissza, egészen addig, amíg a gyorsítótárazás időköze le nem jár.

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. Tallózzon az APIM-példányra.
3. Válassza az **API** lapot.
4. Kattintson a **Demo Conference API** elemre az API-k listájában.
5. Válassza a **GetSpeakers** lehetőséget.
6. A képernyő felső részén válassza a **Tervezés** lapot.
7. A **Bejövő feldolgozás** szakaszban kattintson a **</>** ikonra.

    ![kódszerkesztő](media/api-management-howto-cache/code-editor.png)

8. Az **inbound** elemben adja hozzá a következő szabályzatot:

   ```
   <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
       <vary-by-header>Accept</vary-by-header>
       <vary-by-header>Accept-Charset</vary-by-header>
       <vary-by-header>Authorization</vary-by-header>
   </cache-lookup>
   ```

9. Az **outbound** elemben adja hozzá a következő szabályzatot:

   ```
   <cache-store duration="20" />
   ```

    Az **Időtartam** megadja a gyorsítótárazott válaszok lejárati időközét. Ebben a példában az időköz **20** másodperc.

> [!TIP]
> Ha külső gyorsítótárat használ, a következő témakörben leírtak szerint: [külső Azure cache használata az Azure-beli Redis-API Management](api-management-howto-cache-external.md)esetén érdemes megadnia a `caching-type` gyorsítótárazási házirendek attribútumát. További részletekért tekintse meg [API Management gyorsítótárazási házirendeket](api-management-caching-policies.md) .

## <a name="call-an-operation-and-test-the-caching"></a><a name="test-operation"> </a>Művelet meghívása és a gyorsítótárazás tesztelése
A gyorsítótárazás működés közbeni megtekintéséhez hívja meg a műveletet a fejlesztői portálról.

1. Az Azure Portalon tallózzon az APIM-példányra.
2. Válassza az **API-k** lapot.
3. Válassza ki az API-t, amelyhez gyorsítótárazási házirendeket adott hozzá.
4. Válassza a **GetSpeakers** műveletet.
5. Kattintson a **Tesztelés** lapra a jobb felső menüben.
6. Kattintson a **Küldés** gombra.

## <a name="next-steps"></a><a name="next-steps"> </a>Következő lépések
* További információt a gyorsítótárazási házirendekről az [API Management házirend-referencia][API Management policy reference] oktatóanyag [Gyorsítótárazási házirendek][Caching policies] szakaszában talál.
* További információ az elemeknek a házirend-kifejezések kulcsával történő gyorsítótárazásáról: [Egyéni gyorsítótárazás az Azure API Management szolgáltatásban](api-management-sample-cache-by-key.md).
* További információ a Redis külső Azure cache-ről való használatáról: [külső Azure-gyorsítótár használata az azure API Management-ben való Redis](api-management-howto-cache-external.md).

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md

[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-caching-policies.md

[Create an API Management service instance]: get-started-create-service-instance.md

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps
