---
title: Előfizetések az Azure API Managementban | Microsoft Docs
description: Ismerje meg az Azure API Management-előfizetések koncepcióját. A felhasználók az Azure API Management-előfizetések használatával férhetnek hozzá az API-khoz.
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
ms.openlocfilehash: cdc7668b06308bd5532f4885d0e1365fc34ae989
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97511246"
---
# <a name="subscriptions-in-azure-api-management"></a>Előfizetések az Azure API Managementben

Az előfizetések az Azure API Management egyik fontos fogalma. Ezek a leggyakoribb módszer az API-felhasználók számára, hogy hozzáférést kapjanak egy API Management-példányon közzétett API-khoz. Ez a cikk áttekintést nyújt a koncepcióról.

## <a name="what-are-subscriptions"></a>Mik azok az előfizetések?

Ha API Managementon keresztül tesz közzé API-kat, egyszerűen és közösen biztosíthatja az API-k elérését az előfizetési kulcsok használatával. A közzétett API-kat használó fejlesztőknek érvényes előfizetési kulcsot kell tartalmazniuk a HTTP-kérelmekben, amikor hívásokat kezdeményeznek az API-khoz. Ellenkező esetben a rendszer azonnal visszautasítja a hívásokat a API Management átjáró. Nincsenek továbbítva a háttér-szolgáltatásoknak.

Az API-k eléréséhez szükséges előfizetési kulcs beszerzéséhez előfizetésre van szükség. Az előfizetések lényegében egy megnevezett tárolók egy pár előfizetési kulcshoz. A közzétett API-kat használó fejlesztők előfizetéseket kaphatnak. És nincs szükségük az API-közzétevők jóváhagyására. Az API-közzétevők közvetlenül az API-felhasználók számára is létrehozhatnak előfizetéseket.

> [!TIP]
> A API Management az API-khoz való hozzáférés biztosítására szolgáló egyéb mechanizmusokat is támogat, beleértve az alábbi példákat:
> - [OAuth 2.0](api-management-howto-protect-backend-with-aad.md)
> - [Ügyféltanúsítványok](api-management-howto-mutual-certificates-for-clients.md)
> - [Hívó IP-címeinek korlátozása](./api-management-access-restriction-policies.md#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>Előfizetések hatóköre

Az előfizetések különböző hatókörökhöz társíthatók: termék, minden API vagy egy egyéni API.

### <a name="subscriptions-for-a-product"></a>Termék előfizetései

A API Management-előfizetések általában egyetlen [API-termék](api-management-terminology.md) hatóköréhez voltak társítva. A fejlesztők megtalálták a termékek listáját a fejlesztői portálon. Ezután előfizetik a használni kívánt termékek előfizetési kérelmeit. Az előfizetési kérések jóváhagyása után vagy automatikusan vagy API-közzétevők használatával a fejlesztő a termékben lévő összes API eléréséhez használhatja a kulcsokat. A fejlesztői portál jelenleg csak a felhasználói profil szakaszban szereplő termék-hatókörű előfizetéseket jeleníti meg. 

![Termék-előfizetések](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> Bizonyos esetekben előfordulhat, hogy az API-közzétevők előfizetések nélkül szeretnék közzétenni egy API-terméket a nyilvánosság számára. A Azure Portalban lévő termék **Beállítások** lapján törölje az **előfizetés megkövetelése** beállítást. Ennek eredményeképpen a termékben található összes API API-kulcs nélkül elérhető.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>Előfizetések minden API-hoz vagy egyedi API-hoz

Ha bevezetjük API Management [fogyasztási](https://aka.ms/apimconsumptionblog) szintjét, néhány módosítást végeztünk a kulcskezelő szolgáltatás egyszerűsítése terén:
- Először is felvettünk két további előfizetési hatókört: az összes API-t és egyetlen API-t. Az előfizetések hatóköre már nem korlátozódik egy API-termékre. Mostantól lehetőség van olyan kulcsok létrehozására, amelyek hozzáférést biztosítanak egy adott API-hoz vagy az API Management-példányon belüli összes API-hoz anélkül, hogy létre kellene hoznia egy terméket, és hozzá kell adnia az API-kat. Emellett minden API Management-példányhoz egy nem módosítható, összes API-előfizetés tartozik. Ez az előfizetés megkönnyíti és egyszerűbbé teszi az API-k tesztelését és hibakeresését a tesztelési konzolon belül.

- Másodszor, API Management mostantól lehetővé teszi az **önálló** előfizetések használatát. Az előfizetések már nem szükségesek egy fejlesztői fiókhoz való társításhoz. Ez a funkció olyan forgatókönyvekben hasznos, mint például amikor több fejlesztő vagy csapat osztozik egy előfizetésen.

- Végezetül az API-közzétevők mostantól közvetlenül is [létrehozhatnak előfizetéseket](api-management-howto-create-subscriptions.md) a Azure Portalban:

    ![Rugalmas előfizetések](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>Következő lépések
További információ a API Managementról:

+ Ismerje meg a API Management egyéb [fogalmait](api-management-terminology.md) .
+ A API Management megismeréséhez kövesse az [oktatóanyagokat](import-and-publish.md) .
+ Gyakori kérdésekért tekintse meg a [gyakran ismételt kérdések oldalát](api-management-faq.md) .
